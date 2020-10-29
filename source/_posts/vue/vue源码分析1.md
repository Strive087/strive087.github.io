---
layout: post
title: vue源码分析-beforeCreate之前
date: 2020-10-13 08:05:49
tags: [vue]
categories: [vue]
---

{% p subtitle, _init()方法 %}
该\_init 方法在 initMixin 中定义,\_init 方法执行了一系列初始化操作
{% codeblock lang:javascript %}
let uid = 0

Vue.prototype._init = function(options) {

const vm = this
vm._uid = uid++ // 唯一标识

vm.$options = mergeOptions(  // 合并options
    resolveConstructorOptions(vm.constructor),
    options || {},
    vm
  )
  ...
  initLifecycle(vm) // 开始一系列的初始化
  initEvents(vm)
  initRender(vm)
  callHook(vm, 'beforeCreate')
  initInjections(vm)
  initState(vm)
  initProvide(vm)
  callHook(vm, 'created')
  ...
  if (vm.$options.el) {
    vm.$mount(vm.$options.el)
  }
}

{% endcodeblock %}
先需要交代下，每一个组件都是一个Vue构造函数的子类，这个之后会说明为何如此。从上往下我们一步步看，首先会定义\_uid属性，这是为每个组件每一次初始化时做的一个唯一的私有属性标识，有时候会有些作用。

{% p subtitle, 合并options %}
回到主线任务，接着会合并options并在实例上挂载一个$options属性。这里是分两种情况的：

1.初始化new Vue

在执行new Vue构造函数时，参数就是一个对象，也就是用户的自定义配置；会将它和vue之前定义的原型方法，全局API属性；还有全局的Vue.mixin内的参数，将这些都合并成为一个新的options，最后赋值给一个的新的属性$options。

2.子组件初始化

如果是子组件初始化，除了合并以上那些外，还会将父组件的参数进行合并，如有父组件定义在子组件上的event、props等等。
经过合并之后就可以通过this.$options.data访问到用户定义的data函数，this.$options.name访问到用户定义的组件名称，这个合并后的属性很重要，会被经常使用到。

{% p subtitle, initLifecycle %}
initLifecycle(vm): 主要作用是确认组件的父子关系和初始化某些实例属性。
{% codeblock lang:javascript %}
export function initLifecycle(vm) {
  const options = vm.$options  // 之前合并的属性
  
  let parent = options.parent;
  if (parent && !options.abstract) { //  找到第一个非抽象父组件
    while (parent.$options.abstract && parent.$parent) {
      parent = parent.$parent
    }
    parent.$children.push(vm)
  }
  
  vm.$parent = parent  // 找到后赋值
  vm.$root = parent ? parent.$root : vm  // 让每一个子组件的$root属性都是根组件
  
  vm.$children = []
  vm.$refs = {}
  
  vm._watcher = null
  ...
  vm._isDestroyed = false
  vm._isBeingDestroyed = false
}
{% endcodeblock %}

{% p subtitle, initEvents %}
initEvents(vm): 主要作用是将父组件在使用v-on或@注册的自定义事件添加到子组件的事件中心中。
{% codeblock lang:javascript %}
export function initEvents (vm) {
  vm._events = Object.create(null)  // 事件中心
  ...
  const listeners = vm.$options._parentListeners  // 经过合并options得到的
  if (listeners) {
    updateComponentListeners(vm, listeners) 
  }
}
{% endcodeblock %}
在经历过合并options阶段后，子组件就可以从vm.$options._parentListeners读取到父组件传过来的自定义事件：
\<child-components @select='handleSelect' />
复制代码传过来的事件数据格式是{select:function(){}}这样的，在initEvents方法内定义vm._events用来存储传过来的事件集合。
内部执行的方法updateComponentListeners(vm, listeners)主要是执行updateListeners方法。这个方法有两个执行时机，首先是现在的初始化阶段，还一个就是最后patch时的原生事件也会用到。它的作用是比较新旧事件的列表来确定事件的添加和移除以及事件修饰符的处理，现在主要看自定义事件的添加，它的作用是借助之前定义的$on，$emit方法，完成父子组件事件的通信，(详细的原理说明会在之后的全局API章节统一说明)。首先使用$on往vm.events事件中心下创建一个自定义事件名的数组集合项，数组内的每一项都是对应事件名的回调函数，例如：
vm._events.select = [function handleSelect(){}, ...]  // 可以有多个
复制代码注册完成之后，使用$emit方法执行事件：
this.$emit('select')
复制代码首先会读取到事件中心内$emit方法第一个参数select的对象的数组集合，然后将数组内每个回调函数顺序执行一遍即完成了$emit做的事情。

{% p subtitle, initRender %}
initRender(vm): 主要作用是挂载可以将render函数转为vnode的方法。
{% codeblock lang:javascript %}
export function initRender(vm) {
  vm._vnode = null
  ...
  vm._c = (a, b, c, d) => createElement(vm, a, b, c, d, false)  //转化编译器的
  vm.$createElement = (a, b, c, d) => createElement(vm, a, b, c, d, true)  // 转化手写的
  ...
}
{% endcodeblock %}
主要作用是挂载vm._c和vm.$createElement两个方法，它们只是最后一个参数不同，这两个方法都可以将render函数转为vnode，从命名大家应该可以看出区别，vm._c转换的是通过编译器将template转换而来的render函数；而vm.$createElement转换的是用户自定义的render函数,例如:
{% codeblock lang:javascript %}
new Vue({
  data: {
    msg: 'hello Vue!'
  },
  render(h) { // 这里的 h 就是vm.$createElement
    return h('span', this.msg);  
  }
}).$mount('#app');
{% endcodeblock %}

{% p subtitle, callHook-beforeCreate %}

在beforeCreate钩子内通过this是不可以访问到data中定义的变量的，因为在vue初始化阶段，这个时候data中的变量还没有被挂载到this上，这个时候访问值会是undefined。不过可以通过this.$option.data()进行访问。beforeCreate这个钩子在平时业务开发中用的比较少，而像插件内部的instanll方法通过Vue.use方法安装时一般会选在beforeCreate这个钩子内执行，vue-router和vuex就是这么干的。

实例的第一个生命周期钩子阶段的初始化工作完成了，一句话来主要说明下他们做了什么事情：

- initLifecycle(vm)：确认组件(也是vue实例)的父子关系
- initEvents(vm)：将父组件的自定义事件传递给子组件
- initRender(vm)：提供将render函数转为vnode的方法
- beforeCreate：执行组件的beforeCreate钩子函数
