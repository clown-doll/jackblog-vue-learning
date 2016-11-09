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

在 index.js 中引入了 App.vue 文件，这个文件定义了整个网站的主体框架。

```
<template>
    <!-- 页面总体框架 -->
    <div class="top-box">
        <!-- 侧边栏 -->
        <Navbar></Navbar>
        <!-- 主体部分 -->
        <router-view></router-view>
        <!-- 消息提示 -->
        <Toaster></Toaster>
    </div>
</template>
```

