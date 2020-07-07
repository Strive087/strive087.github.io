---
title : npm切换镜像源
date : 2020/6/7 20:20:35
tag : [npm,nodejs]
categories : nodeJs
---

- 切换为淘宝镜像

  1.临时使用
{% codeblock lang:sh highlight:false line_number:false %}
npm --registry https://registry.npm.taobao.org install express;
{% endcodeblock %}
  2.持久使用
{% codeblock lang:sh highlight:false line_number:false %}
npm config set registry https://registry.npm.taobao.org
{% endcodeblock %}
  3.通过cnpm
{% codeblock lang:sh highlight:false line_number:false %}
npm install -g cnpm --registry=https://registry.npm.taobao.org
{% endcodeblock %}
- 切换官方镜像
{% codeblock lang:sh highlight:false line_number:false %}
npm config set registry https://registry.npmjs.org/
{% endcodeblock %}
- 查看当前npm源地址
{% codeblock lang:sh highlight:false line_number:false %}
npm config get registry
{% endcodeblock %}


