---
layout: post
title: JS继承
date: 2020-06-11 07:05:49
tags: [javascript]
categories : [javascript]
---
{% p subtitle, 原型链 %}
原型链实现继承的主要方式就是使一个构造函数的原型对象等于另一个构造函数的实例。
{% codeblock lang:javascript %}
function father(){
    this.friend = [1,2,3];
}
function children(){
    this.age = 18;
}
children.prototype = new father();
var person = new children();
{% endcodeblock %}
{% image https://pigbro.online:9608/images/javascript/javascript_inherit.png, 300px %}
但是使用原型链实现继承存在两个问题，一个是在js创建对象中提到的原型对象的引用类型属性共享的问题，另一个是在创建子类型的实例时，无法向超类型的构造函数中传递参数。
{% codeblock lang:javascript %}
function father(){
    this.friend = [1,2,3];
}
function children(){
    this.age = 18;
}
children.prototype = new father();
var person1 = new children();
var person2 = new children();
person1.friend.push(4);
alert(person2.friend); //[1,2,3,4]
{% endcodeblock %}

{% p subtitle, 借用构造函数 %}
借用构造函数实现的继承成功解决了上述原型链实现继承存在的两个问题。
{% codeblock lang:javascript %}
function father(name){
    this.name = name;
    this.friend = [1,2,3];
}
function children(name){
    father.call(this,name);
    this.age = 18;
}
children.prototype = new father();
var person1 = new children('zhu');
var person2 = new children('duan');
person1.friend.push(4);
alert(person1.name); //'zhu'
alert(person2.name); //'duan'
alert(person1.friend); //[1,2,3,4]
alert(person2.friend); //[1,2,3]
{% endcodeblock %}
但是仅仅使用借用构造函数，那么也将无法避免构造函数模式存在的定义方法复用的问题，在js创建对象中有提及。

{% p subtitle, 组合继承 %}
{% codeblock lang:javascript %}
function father(name){
    this.name = name;
    this.friend = [1,2,3];
}
father.prototype.sayname = function(){
    alert(this.name);
}
function children(name,age){
    father.call(this,name);
    this.age = age;
}
children.prototype = new father('zhu');
children.prototype.constructor = children;
children.prototype.sayage = function(){
    alert(this.age);
}
var person1 = new children('duan',19);
var person2 = new children('lei',18);
person1.friend.push(4);
person1.sayname(); //'duan'
person2.sayname(); //'lei'
person1.sayage(); //19
person2.sayage(); //18
alert(person1.friend); //[1,2,3,4]
alert(person2.friend); //[1,2,3]
{% endcodeblock %}
{% gallery %}
![](https://pigbro.online:9608/images/javascript/javascript_inherit1.png)
![](https://pigbro.online:9608/images/javascript/javascript_inherit2.png)
{% endgallery %}
