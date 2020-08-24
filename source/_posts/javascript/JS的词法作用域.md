---
layout: post
title: JS的作用域
date: 2020-06-11 07:05:49
tags: [javascript,作用域]
categories : [javascript,作用域]
---
{% p subtitle, 作用域 %}
其实作用域就是一套用来存储变量以及快速查找变量的一套的规则。分为全局、函数、块级作用域这三种。
块级作用域可通过新增命令let和const声明，所声明的变量在指定块的作用域外无法被访问。块级作用域在如下情况被创建：

1. 在一个函数内部
2. 在一个代码块（由一对花括号包裹）内部

作用域是分层的，内层作用域可以访问外层作用域的变量，反之则不行。
{% image https://zhuduanlei-1256381138.cos.ap-guangzhou.myqcloud.com/uPic/169590b8c66f551b.jpg, 300px %}

{% p subtitle, 词法作用域 %}

{% p subtitle, 关于自由变量的取值 %}
{% codeblock lang:javascript %}
var x = 10
function fn() {
  console.log(x)
}
function show(f) {
  var x = 20
  (function() {
    f() //10，而不是20
  })()
}
show(fn)
{% endcodeblock %}
在fn函数中，取自由变量x的值时，要到哪个作用域中取？——要到创建fn函数的那个作用域中取，无论fn函数将在哪里调用。
{% codeblock lang:javascript %}
var a = 10
function fn() {
  var b = 20
  function bar() {
    console.log(a + b) //30
  }
  return bar
}
var x = fn(),
  b = 200
x() //bar()
{% endcodeblock  %}
fn()返回的是bar函数，赋值给x。执行x()，即执行bar函数代码。取b的值时，直接在fn作用域取出。取a的值时，试图在fn作用域取，但是取不到，只能转向创建fn的那个作用域中去查找，结果找到了,所以最后的结果是30