---
layout: post
title: JS闭包
date: 2020-08-02 17:23:19
tags: [javascript]
categories : [javascript]
---
{% p subtitle, 闭包 %}
闭包时指有权访问另一个函数作用域中的变量的函数。例如下图，createPerson可以访问到createFunc中的变量name。
{% codeblock lang:javascript %}
function createFunc(name){
    return function(age){
        var person = {};
        person.name = name;
        person.age = age
        return person;
    };
}
var createPerson = createFunc('zhu');
var person = createPerson(18);
alert(person.name)  //'zhu'
createPerson = null;
{% endcodeblock %}
{% image https://pigbro.online:9608/images/javascript/javascript_closure.png, 500px %}
由于闭包会携带包含他的函数作用域，也因此他会比其他函数占用更多的内存，所有不要过度使用闭包。

{% p subtitle, 常见的闭包与变量的陷阱 %}
由于闭包在执行的时候，他只会取得包含函数中任何变量的最终值。
{% codeblock lang:javascript %}
var funcArr = [];
for(var i = 0; i < 5; i++){
    funcArr[i] = function(){
        return i;
    };
}
{% endcodeblock %}
所以在执行funcArr数组中的闭包时，然会的i全为5。这时我们可以通过创建一个匿名函数去立即执行返回一个匿名函数。
{% codeblock lang:javascript %}
var funcArr = [];
for(var i = 0; i < 5; i++){
    funcArr[i] = (function(num){
        return function(){
            return num;
        };
    })(i);
}
{% endcodeblock %}

{% p subtitle, 内存泄漏 %}
在ie9之前对于js和dom对象使用不同的垃圾收集例程。因此如果闭包的作用域中保存着一个html元素，那么该元素将无法销毁。
{% codeblock lang:javascript %}
var element = document.getElementById('one');
elememt.onclick = function(){
    alert(elememt.id);
}
{% endcodeblock %}

那么这时可以采用如下方法解决
{% codeblock lang:javascript %}
var element = document.getElementById('one');
var id = elememt.id;
elememt.onclick = function(){
    alert(id);
}
elememt = null;
{% endcodeblock %}
