# 导航栏

整个项目是响应式的，所以导航这边 template 部分也有针对大小分辨率做些结构上的变化。大家在不同分辨率下查看页面，再对照代码查看，就能很清晰的明白整个结构。

但是有个疑问，笔者在小分辨率下看，并没有看到登陆后，下拉的一个结构（退出，设置），但是页面代码中却有这块。引用了一个 dropdown 的组件，利用 [vue-strap](https://github.com/yuche/vue-strap) 插件做的下拉菜单的效果。vue-strap 插件基本就是实现了 Bootstrap 的效果，只不过不需要额外的加载其他第三方库。有兴趣的同学可以去看下其官网的介绍。

那针对 html 结构这块，就不做其他说明了。

除此之外，导航栏组件还有几个主要功能：

* 切换浏览模式（日间模式\/夜间模式）

* 登录状态认证，获取用户信息


### 切换浏览模式

模式的切换，主要由操作切换按钮触发。

```
<a class="pull-right navbar-item change-mode" href="javascript:;" @click="changeMode()">
    <i v-if="styleMode === 'day-mode'" class="fa fa-moon-o"></i>
    <i v-else class="fa fa-sun-o"></i>
</a>
```

点击会执行 `changeMode()` 方法。

我们的组建定一个了这个方法：

```
...
methods: {
    changeMode(){
        this.changeStyleMode()
        document.body.className = this.styleMode
    }
}
...
```

这里就涉及到我们前面章节提到的 vuex 状态管理，浏览模式是各组件公用的一个状态。

this.changeStyleMode\(\) 是哪来的呢？

```
import { changeStyleMode,logout,getUserInfo } from '../vuex/actions' 

export default {
    ...
    vuex:{
        ...
        actions:{
          changeStyleMode,
          ...
        }
    },
    ...
}
```

Vuex actions 是用于分发 mutations 的函数。它不会直接对 store 做修改，通过 dispatch 一个类型为 `CHANGE_STYLE_MODE` 的 mutation 来做修改

```
// ../vuex/actions.js

...
export const changeStyleMode = ({dispatch}) => {
    dispatch(types.CHANGE_STYLE_MODE)
}
...
```

```
// ../vuex/modules/global.val.js

...
[CHANGE_STYLE_MODE](state){
    state.styleMode = (state.styleMode === 'day-mode')?'night-mode':'day-mode'
    saveCookie('styleMode', state.styleMode)
}
...
```

组件通过 getter 从 store 里读取数据：

```
...
    vuex:{
        // 获取值
        getters:{
            ...
            styleMode: state => state.globalVal.styleMode
        }
        ...
    }
...
```

最终页面显示我们需要的浏览模式。

还有一个值得注意的点是，在每次编译前，页面就会去读取 styleMode 的值，从而正确显示 cookies 里存储的状态：

```
...
beforeCompile(){
    // 编译开始前调用
    document.body.className = this.styleMode
}
...
```

### 登录状态认证

整个登录状态的流程处理跟上面浏览模式的流程是一样的，就不再多做说明。这里需要作说明的就是 API 数据的获取。

```
// ../vuex/actions.js

import api from '../api'
...
export const getUserInfo = ({ dispatch }) => {
    api.getMe().then(response => {
        if(!response.ok){
            return dispatch(types.USERINFO_FAILURE)
        }
        dispatch(types.USERINFO_SUCCESS, { user: response.data })
    }, response => {
        dispatch(types.USERINFO_FAILURE)
    })
}
...
```

actions.js 引入了 ..\/api\/index.js，这里定义了获取数据的方法：

```
// ../api/index.js

import {UserResource,AuthResource,ArticleResource,TagResource,MobileResource,CommentResource} from './resources'

...
getMe: function () {
    return UserResource.get({id:'me'})
}
...
```

在 resources.js 里利用 [vue-resource](https://github.com/pagekit/vue-resource) 插件，通过 XMLHttpRequest 或 JSONP 发起请求并处理响应：

```
import Vue from 'vue'
import VueResource from 'vue-resource'
import {API_ROOT} from '../config'
import { getCookie,signOut } from '../utils/authService'

Vue.use(VueResource)

// HTTP相关
Vue.http.options.crossOrigin = true
Vue.http.options.credentials = true

// 拦截器 —— 在请求发送前和发送请求后做一些处理
Vue.http.interceptors.push((request, next)=>{
  // 这里对请求体进行处理（请求发送前的处理逻辑）
  request.headers = request.headers || {}
  if (getCookie('token')) {
    request.headers.Authorization = 'Bearer ' + getCookie('token').replace(/(^\")|(\"$)/g, '')
  }
  next((response) => {
    // 这里可以对响应的结果进行处理（请求发送后的处理逻辑）
    if (response.status === 401) {
      signOut()
      window.location.pathname = '/login'
    }
  })
})

export const UserResource = Vue.resource(API_ROOT + 'users{/id}')
...
```



