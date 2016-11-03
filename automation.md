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

gulp-util 是 gulp 常用的工具库，其中最常用的应该就是log了。



