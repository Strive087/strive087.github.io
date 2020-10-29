---
layout: post
title: vue面试题
date: 2020-10-18 08:05:49
tags: [vue]
categories: [vue]
---
{% p subtitle, runtime和完整版这两个版本的区别 %}
答:最明显的就是大小的区别,还有就是编译的时机不同，完整版同时包含编译器和运行时的版本,编译器是运行时编译，性能会有一定的损耗；运行时版本是借助loader做的离线编译，运行性能更高。

- 编译器：用来将模板字符串编译成为 JavaScript 渲染函数的代码
- 运行时：用来创建 Vue 实例、渲染并处理虚拟 DOM 等的代码。基本上就是除去编译器的其它一切

{% p subtitle, methods内的方法可以使用箭头函数么，会造成什么样的结果 %}
答:是不可以使用箭头函数的，因为箭头函数的this是定义时就绑定的。在vue的内部，methods内每个方法的上下文是当前的vm组件实例，methods[key].bind(vm)，而如果使用使用箭头函数，函数的上下文就变成了父级的上下文，也就是undefined了，结果就是通过undefined访问任何变量都会报错。

{% p subtitle,请问可以在beforeCreate钩子内通过this访问到data中定义的变量么，为什么以及请问这个钩子可以做什么？ %}
答:是不可以直接访问的，因为在vue初始化阶段，这个时候data中的变量还没有被挂载到this上，这个时候访问值会是undefined。不过可以通过this.$options.data()方法获得。beforeCreate这个钩子在平时业务开发中用的比较少，而像插件内部的instanll方法通过Vue.use方法安装时一般会选在beforeCreate这个钩子内执行，vue-router和vuex就是这么干的。
