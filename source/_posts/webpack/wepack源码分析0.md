---
layout: post
title: webpack4.x/5.x源码分析1
date: 2020-10-26 07:05:49
tags: [webpack]
categories: [webpack]
---

{% p subtitle, 单文件输出分析 %}
1.webpack4.x
{% codeblock lang:JavaScript %}
(function (modules) {
  // webpackBootstrap
  // The module cache
  var installedModules = {};

  // The require function
  function __webpack_require__(moduleId) {
    // Check if module is in cache
    if (installedModules[moduleId]) {
      return installedModules[moduleId].exports;
    }
    // Create a new module (and put it into the cache)
    var module = (installedModules[moduleId] = {
      i: moduleId,
      l: false,
      exports: {},
    });

    // Execute the module function
    modules[moduleId].call(
      module.exports,
      module,
      module.exports,
      __webpack_require__
    );

    // Flag the module as loaded
    module.l = true;

    // Return the exports of the module
    return module.exports;
  }
  // Load entry module and return exports
  return __webpack_require__((__webpack_require__.s = './src/index.js'));
})({//立即执行函数传入一个对象,以文件路径为属性名,函数体为值
  './src/index.js': function (module, exports) {
    console.log('zhuduanlei');
  },
});
{% endcodeblock %}
从上面代码可以得知首先调用立即执行函数,然后函数体内会创建一个缓存对象,然后对判断缓存对象中是否存在传入的module,存在就返回,不存在就创建一个module,模拟commonJS的export,然后存入缓存中,最后通过call执行mudule中的函数,返回mudule.export;

2.webpack5.x
{% codeblock lang:JavaScript %}
(() => {
  eval('console.log(zhuduanlei)');
})();
{% endcodeblock %}
由于现代浏览器的普及,在webpack5中便直接将源代码进行输出执行

{% p subtitle, 多文件输出分析 %}