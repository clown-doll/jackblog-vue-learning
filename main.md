# 主体内容

### 结构

从 Home\/index.vue 文件的 template 部分很容易看出主体内容的结构：

```
<template>
    <div class="container-fluid main-box">
        <div class="row">
            <!--主体侧边-->
            <Sidebar :index-img="indexImg"></Sidebar>
            <div class="col-sm-7 col-sm-offset-3 main-content">
                <!--标签-->
                <Tags :tag-list="tagList" :options="options" :is-fetching="isFetching"></Tags>
                <!--文章列表-->
                <Articles :article-list="articleList"></Articles>
                <!--加载更多-->
                <Loadmore v-if="articleList.length > 0 && isMore" :options="options" :is-more="isMore" :is-fetching="isFetching"></Loadmore>
            </div>
        </div>
    </div>
    <!--底部-->
    <Footerbar></Footerbar>
</template>
```

![](/assets/jgt.jpg)

### 数据显示

主体部分内容，基本属于直接展示的，因此进入页面就要去获取数据，渲染显示。

```
...
created(){
    if(this.indexImg === ''){
        this.getIndexImage()
    }
    if(this.tagList.length < 1){
        this.getTagList()
    }
    if(this.articleList.length < 1){
        this.getArticleList(this.options)
    }
}
...
```

