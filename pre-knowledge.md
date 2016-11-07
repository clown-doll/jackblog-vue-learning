# 学前阅读

在这里默认大家对 Vue.js 都有一定了解，就不再去细说这块知识。[Vue.js 官网](https://vuejs.org.cn/)有比较完善的 API ，我们可以在有疑问的时候随时查阅。

Jackblog-Vue 这个项目，是利用 vuex 进行组件状态管理的，对于 Vuex 状态管理的流程，会梳理下。

## [Vuex](http://vuex.vuejs.org/)

Vuex 是专门为 Vue.js 应用所设计的集中式状态管理架构，适用于大中型 SPA 应用。其主要作用是解决中大型应用各组件状态的共用问题。它将状态分为两种：组件本地状态和应用层级状态。

* 组件本地状态：仅在一个组件内使用的状态\(data 字段\)

* 应用层级状态：多个组件共用的状态


Vuex 通过汇总应用的状态管理于一处，避免应用规模大的情况下，父组件和子组件强耦合，各事件传递追踪出现问题。

### 核心概念

* 状态树（Stroe\/State）：包含全部应用层级状态的对象（单一状态树）

* Getters：在组件内部获取 store 中状态的函数

* Mutations：修改状态的事件回调函数

* Actions：组件内部用来分发 mutations 事件的函数


![](/assets/vuex.png)

整个流程是从组件调用开始，数据单向流动，通过 Mutations 修改状态，最终 State 会反映到组件。具体的案例，可以查看 github 上中文 API [简明教程 ](https://github.com/vuejs/vuex/blob/1.0/docs/zh-cn/tutorial.md)。

