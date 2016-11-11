# 分类标签

标签这边的数据传递与侧边背景的数据传递是一样的。都是通过 `props` 传递数据。

这边重点看几个操作：加载，切换，排序。

### 加载

加载发生在刚进入页面或切换数据时，tag 标签旁边会出现 loading 的动画。

![](/assets/loading.png)

整个流程是这样的：先从父组件那边获取 isFetching ，判断是否在加载，如果在加载，显示 loading 图片，data 函数提供数据全新副本。

```
// components/Home/tags.vue

<template>
    ...
    <img v-show="isFetching" class="loader-tiny" :src="loadingImg" alt="Tiny">
    ...
</template>

<script>
import tiny from '../../assets/images/tiny.gif'

export default {
  props: [... 'isFetching'],
  data(){
    return {
      loadingImg: tiny
    }
  }
  ...
}
</script>
```

```
// vuex/modules/article.list.js

...
[REQUEST_ARTICLE_LIST](state){
    state.isFetching = true
}
...
```

获取完文章列表，会改变 isFetching 的值：

```
// vuex/actions.js

//getArticleList
export const getArticleList = ({ dispatch }, options, isAdd) => {
    dispatch(types.REQUEST_ARTICLE_LIST)
    ...
}
...
```

```
// vuex/modules/article.list.js 

...
[GET_ARTICLE_LIST_FAILURE](state){
    state.isFetching = false
}
...
```

### 切换标签

点击标签切换，主要完成两件事情：改变标签选中、加载对应标签文章列表。

**改变标签**

在 tags 组件里定一个了 changeTag\(\) 方法：

```
// components/Home/tags.vue

...
<a :class="{'active':(options.tagId == tag._id)}" @click.prevent="changeTag(tag._id)" href="javascript:;">
    {{tag.name}}
</a>
...

...
changeTag(tagId){
    this.$parent.handleChange({'currentPage':1,'sortName':'','tagId':tagId})
}
...
```

`this.$parent` 指向其父组件，其定一个了 `handleChange()` 方法用于处理切换：

```
// components/Home/index.vue

...
vuex:{
    getters:{
        ...
        options: ({options}) => options.item,
        ...
    },
    actions:{
        ...
        changeOptions,
        ...
    }
}
...

...
handleChange(options,isAdd=false){
    this.changeOptions(options)
    this.getArticleList(this.options,isAdd)
}
...
```

```
// vuex/actions.js

..
//更改options
export const changeOptions = ({ dispatch },options) => {
    dispatch(types.CHANGE_OPTIONS, { options: options })
}
...
```



