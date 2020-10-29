---
layout: post
title: vue源码分析-Vue实例创建
date: 2020-10-12 08:05:49
tags: [vue]
categories: [vue]
---

{% p subtitle, vue实例的生成 %}
{% codeblock lang:javascript %}
import { initMixin } from './init'
import { stateMixin } from './state'
import { renderMixin } from './render'
import { eventsMixin } from './events'
import { lifecycleMixin } from './lifecycle'

function Vue(options) {
...
this._init(options)
}

initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)
{% endcodeblock %}
Vue 不采用 ES6 的 class 来定义，因为这样可以方便的把 vue 的功能拆分到不同的目录中去维护，将 vue 的构造函数传入到以下方法内：

- initMixin(Vue)：定义\_init 方法。
- stateMixin(Vue)：定义数据相关的方法$set,$delete,\$watch 方法。
- eventsMixin(Vue)：定义事件相关的方法$on，$once，$off，$emit。
- lifecycleMixin(Vue)：定义\_update，及生命周期相关的$forceUpdate 和$destroy。
- renderMixin(Vue)：定义\$nextTick，\_render 将 render 函数转为 vnode。

这些方法都是在各自的文件内维护的，从而让代码结构更加清晰易懂可维护。

在再这些 xxxMixin 完成后，接着会定义一些全局的 API:
{% codeblock lang:javascript %}
export function initGlobalAPI(Vue) {
Vue.set 方法
Vue.delete 方法
Vue.nextTick 方法

...

内置组件：
keep-alive
transition
transition-group

...

initUse(Vue)：Vue.use 方法
initMixin(Vue)：Vue.mixin 方法
initExtend(Vue)：Vue.extend 方法
initAssetRegisters(Vue)：Vue.component，Vue.directive，Vue.filter 方法
}

{% endcodeblock %}

这里需要提一下 vue 的架构设计，它的架构是分层式的。最底层是一个 ES5 的构造函数，再上层在原型上会定义一些\_init、\$watch、\_render 等这样的方法，再上层会在构造函数自身定义全局的一些 API，如 set、nextTick、use 等(以上这些是不区分平台的核心代码)，接着是跨平台和服务端渲染(这些暂时不在讨论范围)及编译器。

{% p subtitle, vue变量命名 %}
在 vue 的内部，\_符号开头定义的变量是供内部私有使用的，而\$符号定义的变量是供用户使用的，而且用户自定义的变量不能以\_或\$开头，以防止内部冲突。

{% p subtitle, vue2.x 源码目录结构 %}
{% image https://zhuduanlei-1256381138.cos.ap-guangzhou.myqcloud.com/uPic/%E6%88%AA%E5%B1%8F2020-10-18%20%E4%B8%8B%E5%8D%883.03.59.png, 300px %}

- flow：javaScript 是弱类型语言，使用 flow 以定义类型和检测类型，增加代码的健壮性。
- src/compiler：将 template 模板编译为 render 函数。
- src/core：与平台无关通用的逻辑，可以运行在任何 javaScript 环境下，如 web、Node.js、weex 嵌入原生应用中。
- src/platforms：针对 web 平台和 weex 平台分别的实现，并提供统一的 API 供调用。
- src/core/observer：vue 检测数据数据变化改变视图的代码实现。
- src/core/vdom：将 render 函数转为 vnode 从而 patch 为真实 dom 以及 diff 算法的代码实现。
- dist：存放着针对不同使用方式的不同的 vue 版本。

{% p subtitle, vue版本 %}
{% image https://zhuduanlei-1256381138.cos.ap-guangzhou.myqcloud.com/uPic/%E6%88%AA%E5%B1%8F2020-10-19%20%E4%B8%8B%E5%8D%885.16.23.png, 300px %}

vue 使用的是 rollup 构建的，具体怎么构建的不重要，总之会构建出很多不同版本的 vue。vue-cli 默认是使用运行时版本。按照使用方式的不同，可以分为以下三类:

- UMD：通过\<script>标签直接在浏览器中使用。
- CommonJS：使用比较旧的打包工具使用，如 webpack1。
- ES Module：配合现代打包工具使用，如 webpack2 及以上,分为直接作用于浏览器和基于构建工具使用。

其实运行时版与完整版区别在于以下两点:

- 最明显的就是大小的区别，带编译器会比不带的版本大。
- 编译的时机不同，编译器是运行时编译，性能会有一定的损耗；运行时版本是借助 loader 做的离线编译，运行性能更高。
