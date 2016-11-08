# 源码分析

我们先从 index.js 开始。

注：整个项目都是用 ES6 语法来写的，所以，大家应该要对 ES6 语法有所熟悉。[阮一峰的ES6入门教程](http://es6.ruanyifeng.com/#README)，比较详细的讲解了 ES6 的语法特性，在阅读源码过程中有疑问的时候，可以去查看下。

使用插件

在 Vue.js 中，使用 `Vue.use()` 这个全局方法来使用插件。

    // 调用 `MyPlugin.install(Vue)` 
    Vue.use(MyPlugin)

index.js 使用了 [vue-router](https://github.com/vuejs/vue-router) 路由插件，[vue-validator](https://github.com/vuejs/vue-validator) 表单验证插件，[vuex-router-sync](https://github.com/vuejs/vuex-router-sync) 路由状态管理插件。

## 路由

Jackblog-Vue 这个项目路由是使用 1.0 版本的，这里是[中文版本的 API](https://github.com/vuejs/vue-router/tree/1.0/docs/zh-cn) 。

**调用路由功能**

```
// index.js
Vue.use(VueRouter);  
```



**定义路由映射表**

Vue-router 插件使用 `router.map()` 方法定义路由。

在中大型项目中通常会将路由映射表独立出来，我们这个项目也是这样的：

```
// routes.js
export default function(router) {
  router.map({
      '/': {
        name: 'home',
        component: require('./components/Home/index.vue')
      },
      '/login': {
        name: 'login',
        component: require('./components/Login/index.vue')
      },
      '/settings': {
        name: 'settings',
        component: require('./components/Settings/index.vue')
      },
      '/article/:aid': {
        name: 'article',
        component: require('./components/Article/index.vue')
      },
      '/apps': {
        name: 'apps',
        component: require('./components/Apps/index.vue')
      },
      '*': {
        component: require('./components/NotFound.vue')
      }
    })
    // router.beforeEach((transition)=>{
    //   transition.next()
    // })
}
```

在这个文件中，通过 `export default` 输出模块，以供 index.js 引用。从上面的路由映射表也能够看出我们整个项目的一个结构，后续我们将按照浏览顺序依次讲解。



传入路由映射配置
