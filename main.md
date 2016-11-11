## 主体内容结构

从 Home\/index.vue 文件的 template 部分很容易看出主体内容的结构：

```
<template>
    <div class="container-fluid main-box">
        <div class="row">
            <!--首页大背景图-->
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

![](/assets/jg.jpg)





