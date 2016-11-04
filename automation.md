# 自动化配置

整个项目是通过 gulp 来管理任务的。gulp 里启动 webpack，webpack 对应入口文件 index.js，index.js 生成 vue 实例，利用 vuex 和 vue-router 进行组件状态管理和路由跳转。

## gulpfile.js

gulpfile.js 最要的工作就是启动 webpack 及 webpack-dev-server。

主要定义了三个任务：

* `serve`，对应 package.json 里的 `npm serve` 命令

* `build`，对应 package.json 里的 `npm build` 命令

* `serve:dist`，对应 package.json 里的 `npm start:dist` 命令


### 模块插件介绍

```
var path = require('path');
var gulp = require('gulp');
var gutil = require('gulp-util');
var WebpackDevServer = require('webpack-dev-server');
var webpack = require('webpack');
var del = require('del');
var env = require('gulp-env');
var gulpSequence = require('gulp-sequence');
var nodemon = require('gulp-nodemon');
var open = require('open');
```

**[Path](https://nodejs.org/dist/latest-v6.x/docs/api/path.html)**

Path 是 node.js 的原生模块，用于处理文件路径。

gulpfile.js 里主要是用到 `path.join([...paths])` 方法，用于连接路径。该方法的主要用途在于，会正确使用当前系统的路径分隔符，Unix系统是 "\/"，Windows系统是 "\"。

**[gulp-util](https://www.npmjs.com/package/gulp-util)**

gulp-util 是 gulp 常用的工具库，其中最常用的应该就是 log 了，该方法支持传入多个参数，打印结果会将多个参数用空格连接起来。

在这个项目中，还用到了 gulp-util 的 PluginError 类，将错误加上插件名字作为前缀，帮助定位错误。

[**gulp-env**](https://www.npmjs.com/package/gulp-env)

gulp-env 用于定义环境变量。

[**gulp-sequence**](https://www.npmjs.com/package/gulp-sequence)

gulp 的任务的执行是异步的，默认将并行运行所有任务。如果任务间有前后依赖关系，可能会导致错误。gulp-sequence 插件能很好的解决这个问题，它提供了相应的方法，让我们能按顺序执行多个 gulp 任务。

[**gulp-nodemon**](https://www.npmjs.com/package/gulp-nodemon)

gulp-nodemon 用于重启服务器的插件 。它基本上跟普通的 nodemon 工具一样，只不过是针对 gulp 任务的。

**[del](https://www.npmjs.com/package/del)**

del 用于删除文件夹里的内容。通常在重新构建的时候，会先删除原先文件夹里的内容，再进行构建。

**[open](https://www.npmjs.com/package/open)**

open 用于打开文件或链接。

[**webpack-dev-server**](https://www.npmjs.com/package/webpack-dev-server)

webpack-dev-server 是一个小型的静态文件服务，使用它可以为webpack打包生成的资源文件提供Web服务，进行自动刷新和热替换。[这里](http://www.jianshu.com/p/941bfaf13be1)有关于这个插件更详细的解释，大家可以去看看。

### 主要任务

**serve任务**

`server` 任务主要是启动 `webpack-dev-server` ，开发环境下能够文件修改，自动刷新

```
gulp.task('serve', function() {
    // 获取 webpack 配置对象
    var webpackConfig = require('./webpack.config'); 
    var myConfig = Object.create(webpackConfig);

    // 在 webpack 配置中添加实时更新的配置
    myConfig.entry.unshift('webpack/hot/only-dev-server');
    myConfig.entry.unshift('webpack-dev-server/client?http://localhost:' + DEV_PORT);

    // 启动热替换（无需刷新整个页面，只把变化部分替换）
    // http://webpack.github.io/docs/webpack-dev-server.html
    new WebpackDevServer(webpack(myConfig), {  // 对devserver的配置
        noInfo: false, 
        hot: true, // 开启热替换功能
        inline: true, // 内联模式，该模式下修改代码后，webpack将自动打包并且刷新浏览器
        historyApiFallback: true, // 无刷新更改地址栏
        publicPath: myConfig.output.publicPath, // 注意此处publicPath必填
        stats: {
            colors: true // 终端中输出结果为彩色
        }
    }).listen(DEV_PORT, 'localhost', function(err) { // 开发环境，监听端口
        if (err) throw new gutil.PluginError('webpack-dev-server', err); // 错误捕获
        gutil.log('[webpack-dev-server]', '==> 🌎  http://localhost:' + DEV_PORT); // 打印信息
        open('http://localhost:' + DEV_PORT); // 打开客户端 web 页面
    });
});
```

其中 `webpack.config.js` 主要是根据环境变量来判断是使用开发环境的配置还是生产环境的配置

```
if (process.env.NODE_ENV === 'production') {
    module.exports = require('./webpack.config.prod')
} else {
    module.exports = require('./webpack.config.dev')
}
```

执行 `gulp serve` 命令：（省略构建过程打印信息）

![](/assets/gulp-serve.png)

同时，监听各文件，在开发过程中实时编译刷新。

**build任务**

`build` 任务主要作用就是打包构建：

```
gulp.task('build', gulpSequence('clean','webpack:dist'));
```

前面有提到，gulp-sequence 插件用来控制任务的执行顺序。

这边先执行 `clean` 任务，将 dist 目录下所有内容先清空：

```
gulp.task('clean', function() {
    del([path.join(__dirname, '/dist/*')])
});
```

然后再进行 `webpack:dist` 任务：

```
gulp.task('webpack:dist',gulpSequence('set-env-prod','webpack'));
```

`webpack:dist` 任务又分两个阶段，先执行 `set-env-prod` 任务，设置环境变量：

```
gulp.task('set-env-prod', function() {
    env({
        vars: {
            'NODE_ENV': 'production'
        }
    });
});
```

然后再执行 webpack 任务，利用 webpack 进行打包构建：

```
gulp.task('webpack', function(callback) {
    // 获取 webpack 配置对象
    var config = require('./webpack.config');
    // 启动 webpack
    webpack(config, function(err, stats) {
        if (err) throw new gutil.PluginError("webpack", err);
        gutil.log("[webpack]", stats.toString({
            // output options
        }));
        callback();
    })
});
```

webpack 的相关配置，后面会具体介绍。

执行 `gulp build` 命令：（省略构建过程打印信息）

![](/assets/gulp-build1.png)

![](/assets/gulp-build2.png)

**serve:dist任务**

`serve:dist` 任务主要用于开发环境，在构建的基础上，加上代码监控和自动重启服务器的功能

```
gulp.task('serve:dist',gulpSequence('build','nodemon'));
```

`nodemon` 任务用来监控源代码的任何变化和自动重启服务器：

```
gulp.task('nodemon', function() {
    nodemon({
        script: path.join(__dirname, '/server.js'), // 指定启动js
        ext: 'js', // 监视指定后缀名的文件
        watch: [ // 监视的文件和文件夹
            path.join(__dirname, '/dist')  
        ],
        env: { // 环境和端口
            'NODE_ENV': 'production',
            'PORT': PROD_PORT
        }
    })
});
```

其中，server.js 是生产环境下的启动 server：

```
var path = require('path'); // node原生模块，处理文件路径
var express = require('express'); // 加载express模块
var favicon = require('serve-favicon'); // 设置网站的favicon图标

var app = new express(); // 创建express实例
var port = process.env.PORT || 8400; // 定义端口

app.use(express.static(path.join(__dirname, 'dist'))); // 设置静态目录
app.use(favicon(path.join(__dirname, 'dist', 'favicon.ico'))); // 设置favicon图标路径

app.get("/*", function(req, res) { // 请求
    return res.sendFile(__dirname + '/dist/index.html'); // 根据路径读取指定文件并将内容发送到客户端
})

// 监听端口
app.listen(port, function(err) {
    if (err) {
        console.error(err)
    } else {
        console.info("==> 🌎  Listening on port %s. Open up http://localhost:%s/ in your browser.", port, port)
    }
});
```

以上就是 gulp 的主要功能，用于项目任务管理。自动化工程部分，主要是由 webpack 完成的。

## webpack.config.dev.js

`webpack.config.dev.js` 是开发环境下 webpack 的配置文件。

这边默认大家对 webpack 有所了解，就不做具体说明，只对每个模块做个解释。如果对 webpack 还不是很清晰的，可以查看这边的[入门教程](http://www.cnblogs.com/vajoy/p/4650467.html)以及阮一峰写的 [webpack 学习的 demo](https://github.com/ruanyf/webpack-demos)。

在这个项目中，开发环境下 webpack 主要做了以下几件事情：

* 利用加载器编译处理 js、css、图片、vue文件等

* 自动生成 html 文件

* 独立打包样式文件


```
var path = require('path'); // node原生模块，处理文件路径
var webpack = require('webpack'); // 引入webpack
var HtmlWebpackPlugin = require('html-webpack-plugin'); // 生成html文件插件
var ExtractTextPlugin = require('extract-text-webpack-plugin'); // 独立打包样式文件插件

module.exports = {
    // 开启source-map
    // http://www.cnblogs.com/hhhyaaon/p/5657469.html
    devtool: 'eval-source-map', 
    debug: true,
    // 入口文件配置
    entry: [
        './src/index'  
    ],
    // 输出文件配置
    output: {
        path: process.cwd(), // 返回进程的当前目录（绝对路径）
        filename: 'bundle.js',
        publicPath: '/'
    },
    plugins: [
        // 优化插件,为组件分配ID，通过这个插件webpack可以分析和优先考虑使用最多的模块，并为它们分配最小的ID
        new webpack.optimize.OccurenceOrderPlugin(),
        // 热替换,结合webpack-dev-server
        new webpack.HotModuleReplacementPlugin(),
        // 用来跳过编译时出错的代码并记录，使编译后运行时的包不会发生错误
        new webpack.NoErrorsPlugin(),
        // 生成html文件
        new HtmlWebpackPlugin({
            favicon: path.join(__dirname, 'src/favicon.ico'),
            title: "Jackblog vue版",
            template: path.join(__dirname, 'src/index.html'),
            inject: true
        }),
        // 独立css文件
        new ExtractTextPlugin('[hash:8].style.css', {
            allChunks: true
        })
    ],
    module: {
        // 在loaders执行前处理
        preLoaders: [{
            test: /\.js$/,
            loader: "eslint-loader", // eslint校验
            exclude: /node_modules/
        }],
        // 处理器
        loaders: [
        // 解析.vue文件
        {
            test: /\.vue$/,
            loader: 'vue', 
            include: path.join(__dirname, 'src')
        }, 
        // 转化ES6的语法
        {
            test: /\.js$/,
            loader: 'babel', 
            exclude: /node_modules|vue\/dist|vue-hot-reload-api|vue-router\/|vue-loader/
        }, 
        // 解析并独立样式
        {
            test: /\.css$/,
            loader: ExtractTextPlugin.extract('style-loader', 'css-loader?sourceMap') 
        }, 
        // 图片转化
        {
            test: /\.(jpe?g|png|gif)$/i,
            loaders: [ 
                'url?limit=10000&name=images/[hash:8].[name].[ext]',
                'image-webpack?{progressive:true, optimizationLevel: 7, interlaced: false, pngquant:{quality: "65-90", speed: 4}}'
            ]
        }, 
        // 字体处理
        {
            test: /\.(woff|woff2|ttf|eot|svg)(\?v=[0-9]\.[0-9]\.[0-9])?$/,
            loader: 'url?limit=10000&name=fonts/[hash:8].[name].[ext]'
        }]
    },
    // vue文件配置
    vue: {
        loaders: {
            js: 'babel!eslint'
        }
    },
    // eslint配置
    eslint: {
        configFile: './.eslintrc.json'
    },
    resolve: {
        // 默认module查找路径
        root: path.resolve(__dirname, 'node_modules'), 
        // require时省略的扩展名，如：require('module') 不需要module.js
        extensions: ['', '.js', '.vue', '.scss']
    }
}
```

## webpack.config.prod.js

`webpack.config.prod.js` 是生产环境下 webpack 的配置文件。基本上与开发环境下差不多，我们将有区别的部分单独抽出来分析下。

**入口文件处理**

webpack 会将所有文件都打包成一个 js 文件，在生产环境中，为了避免打包文件过大，通常会过滤掉第三方库，因此，我们可以这样设置：

```
entry: {
    // 提取第三方库，避免打包文件过大
    vendor: ['vue', 'vuex', 'vue-router'],
    // 入口文件
    bundle: './src/index'
}
```

**输出文件处理**

在生产环境下，输出文件的配置也与开发环境有所不同：

```
output: {
    path: path.join(__dirname, 'dist'),
    filename: '[hash:8].[name].js',
    publicPath: '/'
}
```

增加了 `hash` 处理。看过其他案例，使用 `chunkhash` 的居多。`chunkhash` 是根据具体模块文件的内容计算所得的 `hash` 值；`hash` 是项目总体文件的 `hash` 值。二者更多的区别，可以查看[这篇文章](http://www.cnblogs.com/ihardcoder/p/5623411.html)。 



