---
layout: post
title: this指针
date: 2020-08-24 11:31:00
tags: [javascript,this]
categories : [javascript]
---
{% p subtitle, this指针的由来 %}
由于函数可以在不同的运行环境执行，所以需要有一种机制，能够在函数体内部获得当前的运行环境（context）。所以，this就出现了，它的设计目的就是在函数体内部，指代函数当前的运行环境。

{% p subtitle, this指针全局对象调用 %}
{% codeblock lang:javascript %}
function foo(){
    console.log(this);
}
foo();
{% endcodeblock %}
这是的全局环境调用foo函数，那么this指针指向全局对象。
需要注意的是全局对象不是顶层对象。在es5中全局对象等于顶层对象，但在es6中他们分离开了。例如下面例子：
{% codeblock lang:javascript %}
let x = 1;
function foo(){
    console.log(this.x);
}
foo(); //undefined，这是this指向全局对象window而x挂载在顶层对象上
{% endcodeblock %}

{% p subtitle, this指针普通对象调用 %}
{% codeblock lang:javascript %}
function foo(){
    console.log(this);
}
let obj1 = {
    f : foo
}
let obj2 = {
    f : function(){
        foo();
    }
}
obj1.f(); //obj1
obj2.f(); //window
{% endcodeblock %}
在对象环境调用时，this指针指向该对象。但是需要注意的是上面的例子obj2.f()的this，因为obj2.f的值并不是指向foo的地址，而是一个匿名函数的地址。

{% p subtitle, this指针数组环境调用 %}
{% codeblock lang:javascript %}
function foo(){
    console.log(this);
}
let arr = [foo];
arr[0](); // arr
{% endcodeblock %}
在函数放置于数值中进行调用时，this指针指向素组本身。

{% p subtitle, this指针构造函数调用 %}
{% codeblock lang:javascript %}
var x = 2;
function test() {
  this.x = 1;
}

var obj = new test();
obj.x //1
x //2
{% endcodeblock %}
在构造函数中时，this指针指向新对象。

{% p subtitle, this指针箭头函数调用 %}
this的值是可以用call方法修改的，而且只有在调用的时候我们才能确定this的值。而当我们使用箭头函数的时候，箭头函数不会创建自己的this,它只会从自己的作用域链的上一层继承this。
{% codeblock lang:javascript %}
function foo() {
    setTimeout(() => {
        console.log(this.id);
    }, 100);
}
id = 21;
foo.call({ id: 12 });//12

function foo(){
    console.log(this);
}
let obj1 = {
    f : foo
}
let obj2 = {
    f : function(){
        (function(){
            consolt.log(this);
        })()
    }
}
obj1.f(); //window
obj2.f(); //window

function foo(){
    console.log(this);
}
let obj1 = {
    f : foo
}
let obj2 = {
    f : function(){
        (()=>{console.log(this)})()
    }
}
obj1.f(); //window
obj2.f(); //obj2
{% endcodeblock %}
由于箭头函数没有自己的this指针，通过call()或apply()等方法调用一个函数时，只能传递参数，他们的第一个参数会被忽略。
