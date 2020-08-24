---
layout: post
title: XHR的使用
date: 2020-08-23 16:02:00
tags: [javascript,xhr]
categories : [javascript]
---
{% p subtitle, xhr基本用法 %}

1. XMLHttpRequest是原生xhr对象，可使用new操作符生成xhr对象。
2. open方法接受3个参数，发送请求的类型、url和是否异步发送。open方法并未真正发送，只是启动以备发送。
3. send方法接受1个参数，作为请求主体发送的数据，若不需要请求主体发送数据则必须传入null。调用该方法后便会发送请求到服务器，如果请求是同步的，JavaScript代码将等到服务器响应之后再继续执行，收到响应后，响应的数据会自动填入xhr对象属性。
4.xhr对象有个readystatechange事件，可以监听xhr的readyState属性的变化。以下readyState的属性值代表的含义：

    * 0: 未初始化。
    * 1: 启动未发送。
    * 2: 发送。
    * 3: 接收部分数据。
    * 4: 完成接收所有数据。

5. abort方法，在接收到响应数据之前调用该方法可以取消异步请求。

{% p subtitle, http头部请求 %}

1. setRequestHeader方法接受2个参数，头部字段的名称和值。该方法必须在open方法调用之后、send方法调用之前使用才有效。
2. getResponseHeader方法接受1个参数，头部字段名称获取值。
3. getAllResponseHeader获取所有头部信息。

{% p subtitle, 表单数据传达 %}
FormData为序列化表单以及创建与表单格式相同的数据提供了便利。
{% codeblock lang:javascript %}
var xhr = new XMLHttpRequest;
var form = document.getElementById('user-info');
xhr.open(...)
xhr.send(new FormData(form));
{% endcodeblock %}

{% p subtitle, 超时设定 %}
xhr有timeout属性和ontimeout事件，当请求时间到达timeout属性时间后请求就会自动终止，终止时触发ontimeout时间。
需要注意的是在终止之后访问status属性将导致错误。
{% codeblock lang:javascript %}
var xhr = new XMLHttpRequest;
xhr.open(...)
xhr.timeout = 1000;
xhr.ontimeout = function(){...};
xhr.send(null);
{% endcodeblock %}

{% p subtitle, overrideMimeType %}
overrideMimeType方法重写xhr响应的MIME类型。
{% codeblock lang:javascript %}
var xhr = new XMLHttpRequest;
xhr.open(...)
xhr.overrideMimeType("text/xml")
xhr.send(null);
{% endcodeblock %}

ç
进度事件有如下几种：

* onloadstart：接收到响应数据第一个字节触发
* onprogress：在接收响应数据期间不断触发
* onerror：请求发送错误触发
* onabort：调用abort方法触发
* onload：接收到完整响应数据触发
* onloadend：通信完成或者触发error、abort或load事件后触发

其中onprogress事件会接收一个event对象。

* event.target指向xhr对象
* event.lengthComputable是个布尔值代表进度信息是否可用
* event.position表示已经接收的字节数
* event.totalSize表示根据Content-Length响应头部确定的预期字节数 
