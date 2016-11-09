# 总体框架

Vue.js 项目中的所有文件都是以组件的形式出现，每个组件就是一个 `.vue` 文件。一个 vue 文件通常包含它所需的 html，css，javascript，像这样

```
<style>
    /* code */
</style>

<template>
    <!-- code -->
</template>

<script>
    // code
</script>
```

在 index.js 中引入了 App.vue 文件，它是整个项目的根组件，该文件定义了整个网站的主体框架。

```
<template>
    <!-- 页面总体框架 -->
    <div class="top-box">
        <!-- 导航栏 -->
        <Navbar></Navbar>
        <!-- 主体部分 -->
        <router-view></router-view>
        <!-- 消息提示 -->
        <Toaster></Toaster>
    </div>
</template>
```

并且在该根组件中引入了 vuex 的核心文件之一 store.js，该文件定义了全部应用层级状态，即各个组件共享的状态。同时，在跟组件中将其暴露出去，这样就能将 store 引入到子组件里：

```
<script>
    // 加载模块
    import store from '../vuex/store';
    import Navbar from './Navbar';
    import Toaster from './Toaster';

    // 为模块指定默认输出
    export default {
        store,
        components: { Navbar,Toaster }
    }
</script>
```

