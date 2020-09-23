---
title: npm切换镜像源
date: 2020/6/7 20:20:35
tag: [npm, nodejs]
categories: nodeJs
---

{% p subtitle, 手动切换npm源 %}

- 切换为淘宝镜像
  npm install cnpm 1.临时使用
  {% codeblock lang:sh highlight:false line_number:false %}
  npm --registry https://registry.npm.taobao.org install express;
  {% endcodeblock %} 2.持久使用
  {% codeblock lang:sh highlight:false line_number:false %}
  npm config set registry https://registry.npm.taobao.org
  {% endcodeblock %} 3.通过 cnpm
  {% codeblock lang:sh highlight:false line_number:false %}
  npm install -g cnpm --registry=https://registry.npm.taobao.org
  {% endcodeblock %}
- 切换官方镜像
  {% codeblock lang:sh highlight:false line_number:false %}
  npm config set registry https://registry.npmjs.org/
  {% endcodeblock %}
- 查看当前 npm 源地址
  {% codeblock lang:sh highlight:false line_number:false %}
  npm config get registry
  {% endcodeblock %}

{% p subtitle, nrm切换npm源%}
通过 nrm 设置 npm 源
npm install -g nrm
nrm ls
nrm use <registry>

{% p subtitle, npm设置代理 %}
npm 代理:
npm config set proxy http://127.0.0.1:1087
npm config set https-proxy http://127.0.0.1:1087
npm config set proxy socks5://127.0.0.1:1080
npm config set https-proxy socks5://127.0.0.1:1080

取消代理:
npm config delete proxy
npm config delete https-proxy
