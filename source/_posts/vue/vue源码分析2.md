---
layout: post
title: vue源码分析-created之前
date: 2020-10-16 08:05:49
tags: [vue]
categories: [vue]
---
{% p subtitle, initInjections(vm)方法 %}
主要作用是初始化inject，可以访问到对应的依赖。
{% codeblock lang:javascript %}
export function initInjections(vm) {
  const result = resolveInject(vm.$options.inject, vm) // 找结果
  
  ...
}

export function resolveInject (inject, vm) {
  if (inject) {
    const result = Object.create(null)
    const keys = Object.keys(inject)  //省略Symbol情况

    for (let i = 0; i < keys.length; i++) {
      const key = keys[i]
      const provideKey = inject[key].from
      let source = vm
      while (source) {
        if (source._provided && hasOwn(source._provided, provideKey)) { //hasOwn为是否有
          result[key] = source._provided[provideKey]
          break
        }
        source = source.$parent
      }
    ... vue@2.5后新增设置inject默认参数相关逻辑
    }
    return result
  }
}
{% endcodeblock %}
source就是当前的实例，而source._provided内保存的就是当前provide提供的值。首先从当前实例查找，接着将它的父组件实例赋值给source，在它的父组件查找。找到后使用break跳出循环，将搜索的结果赋值给result，接着查找下一个。由于vue是组件式的,所以会先初始化父组件再初始化子组件,所以是先初始化inject再初始化provide

{% p subtitle, initState(vm)方法 %}
初始化会被使用到的状态，状态包括props，methods，data，computed，watch五个选项。

{% codeblock lang:javascript %}
export function initState(vm) {
  ...
  const opts = vm.$options
  if(opts.props) initProps(vm, opts.props)
  if(opts.methods) initMethods(vm, opts.methods)
  if(opts.data) initData(vm)
  ...
  if(opts.computed) initComputed(vm, opts.computed)
  if(opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}

{% endcodeblock %}

1. initProps (vm, propsOptions)
主要作用是检测子组件接受的值是否符合规则，以及让对应的值可以用this直接访问。
{% codeblock lang:javascript %}
function initProps(vm, propsOptions) {  // 第二个参数为验证规则
  const propsData = vm.$options.propsData || {}  // props具体的值
  const props = vm._props = {}  // 存放props
  const isRoot = !vm.$parent // 是否是根节点
  if (!isRoot) {
    toggleObserving(false)
  }
  for (const key in propsOptions) {
    const value = validateProp(key, propsOptions, propsData, vm)
    defineReactive(props, key, value)
    if (!(key in vm)) {
      proxy(vm, `_props`, key)
    }
  }
  toggleObserving(true)
}

{% endcodeblock %}
props是作为父组件向子组件通信的重要方式，而initProps内的第二个参数propsOptions，就是当前实例也就是通信角色里的子组件，它所定义的接受参数的规则。子组件的props规则是可以使用数组形式的定义的，不过再经过合并options之后会被格式化为对象的形式。所以在定义props规则时，直接使用对象格式吧，这也是更好的书写规范。

{% codeblock lang:javascript %}
export function proxy(target, sourceKey, key) {
  Object.defineProperty(target, key, {
    enumerable: true,
    configurable: true,
    get: function () {
      return this[sourceKey][key]
    },
    set: function () {
      this[sourceKey][key] = val
    }
  })
}
{% endcodeblock %}
这里vue内部做了一层代理，将对this.name的访问转而为对this._props.name的访问。

2.initMethods (vm, methods)
主要作用是将methods内的方法挂载到this下。
{% codeblock lang:javascript %}
function initMethods(vm, methods) {
  const props = vm.$options.props
  for(const key in methods) {

    if(methods[key] == null) {  // methods[key] === null || methods[key] === undefined 的简写
      warn(`只定义了key而没有相应的value`)
    }
    
    if(props && hasOwn(props, key)) {
      warn(`方法名和props的key重名了`)
    }
    
    if((key in vm) && isReserved(key)) {
      warn(`方法名已经存在而且以_或$开头`)
    }
    
    vm[key] = methods[key] == null
      ? noop  // 空函数
      : bind(methods[key], vm)  //  相当于methods[key].bind(vm)
  }
}
{% endcodeblock %}

3.initData(vm)
主要作用是初始化data，挂载到this下。有个重要的点，之所以data内的数据是响应式的，是在这里初始化的
{% codeblock lang:javascript %}
function initData (vm: Component) {
  let data = vm.$options.data
  data = vm._data = typeof data === 'function'
    ? getData(data, vm) // 通过data.call(vm, vm)得到返回的对象
    : data || {}
  if (!isPlainObject(data)) { // 如果不是一个对象格式
    data = {}
    warn(`data得是一个对象`)
  }
  const keys = Object.keys(data)
  const props = vm.$options.props  // 得到props
  const methods = vm.$options.methods  // 得到methods
  let i = keys.length
  while (i--) {
    const key = keys[i]
    if (methods && hasOwn(methods, key)) {
      warn(`和methods内的方法重名了`)
    }

    if (props && hasOwn(props, key)) {
      warn(`和props内的key重名了`)
    } else if (!isReserved(key)) { // key不能以_或$开头
      proxy(vm, `_data`, key)
    }
  }
  observe(data, true)
}
{% endcodeblock %}

{% p subtitle,  initProvide(vm)方法 %}
主要作用是初始化provide为子组件提供依赖。
{% codeblock lang:javascript %}
export function initProvide (vm) {
  const provide = vm.$options.provide
  if (provide) {
    vm._provided = typeof provide === 'function'
      ? provide.call(vm)
      : provide
  }
}
{% endcodeblock %}
provide选项应该是一个对象或是函数，所以对它取值即可，就像取data内的值类似

{% p subtitle, callHook-created %}
执行用户定义的created钩子函数，有mixin混入的也一并执行。

分别用一句话来介绍它们主要都干了什么事：

- initInjections(vm)：让子组件inject的项可以访问到正确的值
- initState(vm)：将组件定义的状态挂载到this下。
- initProvide(vm)：初始化父组件提供的provide依赖。
- created：执行组件的created钩子函数
