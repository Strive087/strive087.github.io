---
layout: post
title: JS创建对象
date: 2020-07-15 07:05:49
tags: [javascript]
categories : [javascript]
---
{% p subtitle, 工厂模式 %}
{% codeblock lang:javascript %}
function createPerson(name,age){
    var person = new Object;
    person.name = name;
    person.age = age;
    person.sayname = function(){
        alert(this.name);
    }
    return person;
}
var person = createPerson('zhu',18);
{% endcodeblock %}

{% p subtitle, 构造函数模式 %}
{% codeblock lang:javascript %}
function Person(name,age){
    this.name = name;
    this.age = age;
    this.sayname = function(){
        alert(this.name);
    }
}
var person = new Person('zhu',18);
{% endcodeblock %}
使用new操作符创建新实例必须经历4个阶段:
 1.创建一个新的空对象对象 -- obj = {}
 2.将构造函数的作用域赋给新对象（因此this就指向了该对象）-- obj.__proto__ = Person.prototype
 3.执行构造函数中的代码（为新对象obj添加属性）
 4.判断fn返回值类型(无返回值默认return this)，如果是值类型，返回obj；如果是引用类型，则返回该引用类型的对象。
构造函数的问题：
对于sayname的函数相当于new Function('alert(this.name)'),这就造成不同实例的同名函数不相等，为了解决这个问题可以将函数定义移到构造函数外部。
{% codeblock lang:javascript %}
function Person(name,age){
    this.name = name;
    this.age = age;
    this.sayname = sayname；
}
function sayname(){
    alert(this.name);
}
{% endcodeblock %}
这样做多个对象共享了全局作用域中同一个函数，但是如果函数增多，那么在全局作用域中定义的函数也增多，那么封装的意义何在？好在这些问题在原型模式中解决。

{% p subtitle, 原型模式 %}
原型模式成功封装并共享同一函数，但是在原型对象对于包含引用类型的数据时，不同实例不会像基本值类型那样在实例上添加同名属性，而是共用同一引用类型数据，这也就是原型模式最大的问题。
{% codeblock lang:javascript %}
function Person(){

}
Person.prototype = {
    constructor : Person,
    name : 'zhu',
    age : 18,
    friend : ['fan','hou'],
    sayName : function(){
        alert(this.name)
    }
}
var person1 = new Person();
var person2 = new Person();
person1.friend.push('hang');
alert(person2.friend)   //['fan','hou','hang']
{% endcodeblock %}

{% p subtitle, 组合使用原型模式和构造函数模式 %}
这种混合模式是es5中使用最广泛、认同度最高的一种创建自定义类型的方法。
{% codeblock lang:javascript %}
function Person(name,age){
    this.name = name;
    this.age = age ;
    this.friend = ['fan','hou'];
}
Person.prototype = {
    constructor : Person,
    sayName : function(){
        alert(this.name)
    }
}
var person1 = new Person();
var person2 = new Person();
person1.friend.push('hang');
alert(person1.friend)   //['fan','hou','hang']
alert(person2.friend)   //['fan','hou']
{% endcodeblock %}

{% p subtitle, 动态原型模式 %}
对于混合模式，在有OO语言开发经验的开发人员来说可能会很困惑，那么动态原型模式，将所有信息都封装在了构造函数中，对原型对象的初始化只会在第一次执行时产生。
这里特别强调，在对原型对象初始化时不可使用对象字面量重写原型，因为已经产生了实例，这样会断开实例与原型对象的联系。
{% codeblock lang:javascript %}
function Person(name,age){
    this.name = name;
    this.age = age;
    this.friend = ['fan','hou'];
    if(typeof this.sayname != 'function'){
        Person.prototype.sayname = function(){alert(this.name)};
    }
}
{% endcodeblock %}

{% p subtitle, 寄生构造函数模式 %}
如果前面提到几种模式都不适用，那么可以适用寄生构造函数。
{% codeblock lang:javascript %}
function Person(name,age){
    var person = new Object;
    person.name = name;
    person.age = age;
    person.sayname = function(){
        alert(this.name);
    }
    return person;
}
var person = new Person('zhu',19);
{% endcodeblock %}
这里特别说明下，在构造函数不返回值的情况下，会默认返回新对象实例。在末尾加上return可以重写返回值。但是这里返回的对象与构造函数或者构造函数的原型对象没有任何关系，所以叫做寄生。

{% p subtitle, 稳妥构造函数模式 %}
稳妥对象，没有公共属性，而且方法也不引用this。稳妥构造函数与寄生构造函数最大不同在于，稳妥构造函数模式实例方法不引用this，而且不是用new来调用构造函数。
{% codeblock lang:javascript %}
function Person(name, age) {
    var person = new Object();
    // private members
    var nameUC = name.toUpperCase();
    // public members
    person.sayName = function() {
        alert(name);
    };
    person.sayNameUC = function() {
        alert(nameUC);
    };
    return person;
}
var person = Person("zhu", 18);
person.sayName(); // "zhu"
person.sayNameUC(); // "ZHU"
alert(person.name);  // undefined
alert(person.nameUC);  // undefined
{% endcodeblock %}

{% p subtitle, new操作符发生了什么 %}
{% codeblock lang:javascript %}
function Person(){
    this.name = 'zhu';
    return 1;
}
var person = new Person;
1.var obj = {}
2.obj.__proto__ = Person.prototype
3.Person.call(obj)
4.return obj
{% endcodeblock %}
在JavaScript构造函数中：如果return值类型，那么对构造函数没有影响，实例化对象返回空对象；如果return引用类型（数组，函数，对象），那么实例化对象就会返回该引用类型；
