# npm切换源

- 切换为淘宝镜像

  1.临时使用
  `npm --registry https://registry.npm.taobao.org install express`

  2.持久使用
  `npm config set registry https://registry.npm.taobao.org`

  3.通过cnpm
  `npm install -g cnpm --registry=https://registry.npm.taobao.org`

- 切换官方镜像

  `npm config set registry https://registry.npmjs.org/`

- 查看当前npm源地址

  `npm config get registry`



