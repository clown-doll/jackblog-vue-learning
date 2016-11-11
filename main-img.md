首页背景

Sidebar 组件就是用来显示网站首页侧边背景及其内容说明的。

背景图片是从父组件传下来的，定义在 store 中：

```
// Home/index.vue --> template

<Sidebar :index-img="indexImg"></Sidebar>
```

```
// Home/index.vue  --> script

...
vuex:{
    getters:{
        indexImg: ({globalVal}) => globalVal.indexImg,
        ...
    },
    actions:{
        getIndexImage,
        ...
    }
}
...
```



