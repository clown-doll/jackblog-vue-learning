# 自动化配置

整个项目是通过 gulp 来启动的。gulp 里启动 webpack，webpack 对应入口文件 index.js，index.js 生成 vue 实例，利用 vuex 和 vue-router 进行组件状态管理和路由跳转。

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

[**Path**](https://nodejs.org/dist/latest-v6.x/docs/api/path.html)

Path 是 node.js 的原生模块，用于处理文件路径。

gulpfile.js 里主要是用到 `path.join([...paths])` 方法，用于连接路径。该方法的主要用途在于，会正确使用当前系统的路径分隔符，Unix系统是 "\/"，Windows系统是 "\"。

[**gulp-util**](https://www.npmjs.com/package/gulp-util)

gulp-util 是 gulp 常用的工具库，其中最常用的应该就是 log 了，该方法支持传入多个参数，打印结果会将多个参数用空格连接起来。

在这个项目中，还用到了 gulp-util 的 PluginError 类，将错误加上插件名字作为前缀，帮助定位错误。

**[gulp-env](https://www.npmjs.com/package/gulp-env)**

gulp-env 用于定义环境变量。

**[gulp-sequence](https://www.npmjs.com/package/gulp-sequence)**

gulp 的任务的执行是异步的，默认将并行运行所有任务。如果任务间有前后依赖关系，可能会导致错误。gulp-sequence 插件能很好的解决这个问题，它提供了相应的方法，让我们能按顺序执行多个 gulp 任务。

**[gulp-nodemon](https://www.npmjs.com/package/gulp-nodemon)**

gulp-nodemon 用于重启服务器的插件 。它基本上跟普通的 nodemon 工具一样，只不过是针对 gulp 任务的。

[**del**](https://www.npmjs.com/package/del)

del 用于删除文件夹里的内容。通常在重新构建的时候，会先删除原先文件夹里的内容，再进行构建。

[**open**](https://www.npmjs.com/package/open)

open 用于打开文件或链接。

**[webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server)**

webpack-dev-server 是一个小型的静态文件服务，使用它可以为webpack打包生成的资源文件提供Web服务，进行自动刷新和热替换。[这里](http://www.jianshu.com/p/941bfaf13be1)有关于这个插件更详细的解释，大家可以去看看。

### 主要任务

**serve**

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
    }).listen(DEV_PORT, 'localhost', function(err) { // 监听端口
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



