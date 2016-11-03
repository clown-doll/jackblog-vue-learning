# 目录及项目结构

### 目录结构

```
.
├── README.md           
├── dist                     // 项目build目录
├── logs                     // 生产环境日志目录
├── src                      // 生产目录
│   ├── api                  // API 请求
│   ├── assets               // css 和图片资源
│   ├── components           // 组件
│   ├── utils                // 工具函数
│   └── vuex                     // vuex相关文件, store,action
│   └── config.js            // api url, cookie domain等配置文件
│   └── index.html           // 主页html
│   └── routes.js            // 路由配置
│   └── index.js             // 入口文件
├── .babelrc                 // babel配置
├── .eslintrc.json           // eslint配置
├── gulpfile.babel.js        // gulp配置, 使用gulp做为任务管理
├── History.md               // 更新日志
├── process.json             // pm2配置文件
├── server.js                // 生产环境启动server
├── webpack.config.dev.js    // 开发环境Webpack配置文件
├── webpack.config.js        // Webpack 配置文件
├── webpack-config.prod.js   // 生产环境Webpack 配置文件
.
```

### 项目结构

这边稍微梳理了下整个项目的结构：

![](/assets/lc.png)

图片看的不够清晰，可以在[这里](https://www.processon.com/view/5819c825e4b06e7dcfd2ae2a)看完整结构。



