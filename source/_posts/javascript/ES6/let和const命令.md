---
layout: post
title: let和const命令
date: 2020-08-22 15:43:00
tags: [javascript,es6]
categories : [javascript]
---
{% p subtitle, let %}
let类似于var，不同的地方在于var没有代码块作用域，这样会导致var变量提升，而let成功解决了这样的问题。
需要特别注意的是在for循环中，for的条件部分是个父作用域，而for的循环体是其中的子作用域。
从下面代码可以看出父作用域子作用域中的let声明的i不影响父作用域的let声明的i。
{% codeblock lang:javascript %}
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
for (var i = 0; i < 3; i++) {
  var a = 'abc';
  console.log(i);
}
//abc
for (var i = 0; i < 3; i++) {
  var a = 'abc';
  console.log(i);
}
//abc
{% endcodeblock  %}
ES6 明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。
{% codeblock lang:javascript %}
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
{% endcodeblock  %}
let不允许在相同作用域内，重复声明同一个变量。
{% p subtitle, const %}
const声明的变量，一经声明便不允许修改。
const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。
{% codeblock lang:javascript %}
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
{% endcodeblock  %}
