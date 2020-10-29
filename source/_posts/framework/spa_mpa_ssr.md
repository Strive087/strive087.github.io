---
layout: post
title: 关于SPA/MPA和SSR/CSR
date: 2020-10-28 07:05:49
tags: [framework]
categories: [framework]
---

{% p subtitle, 什么是SPA %}
SPA也就是单页面应用,通过ajax和前端渲染更新页面部分内容而不是进行整体页面加载

{% p subtitle, 什么是MPA %}
MPA也就是多页面应用,不同的内容来自不同的页面,需要整也加载(不同网址)

{% p subtitle, 什么是SSR %}
SSR也就是服务器端渲染,将完整的html内容发送到前端,网页需要重新加载,相对于SPA单页应用他有如下几个优势:

1.首屏加载时间快(ssr可从服务端直出页面)

2.SEO友好(由于搜索引擎需要爬虫抓去html中的关键字,而spa依靠虚拟dom挂载所以爬虫无法获取,除了google等一些搜索引擎能够去抓取js中的关键字)

当然ssr也有很明显的劣势:

1.页面体验不够友好(相对无spa快速切页,由于ssr每次切页都要从服务端渲染页面,所以会造成卡顿延迟)

2.可见不一定可操作(因为js可能还在执行)

3.服务器压力大

{% p subtitle, 什么是CSR %}
CSR指的是前端渲染,常是前端请求后端获取数据后通过js更新页面内容
