---
layout: post
title: JS继承
date: 2020-07-16 09:34:14
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
![](https://pigbro.online:9608/images/javascript/javascript_inherit2.png)
![](https://pigbro.online:9608/images/javascript/javascript_inherit1.png)
{% endgallery %}
组合继承避免了原型链呵借用构造函数的问题，成为最常用的继承模式。

{% p subtitle, 原型式继承 %}
借助原型可以基于已有对象创建新对象。在只想让一个对象与另一个对象类似的情况下这种模式可以胜任，因为它还是存在共享引用类型属性问题。
es5通过新增Object.create()方法规范化了原型式继承。
{% codeblock lang:javascript %}
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}
var person = {
    name : 'zhu',
    friend : [1,2]
}
var person1 = Object.create(person,{
    age : {
       value : 18,
       writable : true,
    },
    sex : {
        value : 'man'
    }
});
{% endcodeblock %}

{% p subtitle, 寄生式继承 %}
创建一个用于封装继承过程的函数，在函数内部增强对象。在主要考虑对象而不是自定义类型和构造函数的情况下，这个模式也是有用的。
{% codeblock lang:javascript %}
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}
var person = {
    name : 'zhu',
    friend : [1,2]
}
function createAnother(another){
    var clone = object(another);
    clone.sayName = function(){
        alert(this.name)
    }
    return clone;
}
var anotherPerson = createAnother(person);
{% endcodeblock %}

{% p subtitle, 寄生组合式继承 %}
回过头看组合式继承的代码，一次在new father()时在children.prototype上创建了father的属性，另一次在children函数内部father.call时在实例上创建了father的属性。
寄生组合式继承相比组合式继承，解决了实例属性在原型链上重复的问题。寄生式组合继承通过借构造函数来继承属性，通过原型链的混成形式来继承方法。
{% codeblock lang:javascript %}
function inheritPrototype(father,children){
    var prototype = Object.crearte(father.prototype); //必须要创建对象
    prototype.constructor = children;   //增强对象
    children.prototype = prototype;   //指定对象
}
function father(name){
    this.name = name;
    this.friend = [1,2,3];
}
function children(name){
    father.call(this,name);
    this.age = 18;
}
inheritPrototype(father,children);
{% endcodeblock %}