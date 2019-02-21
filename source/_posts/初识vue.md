---
title: 初识vue
tags: vue 
---


### 使用`vue-cli`搭建项目
---

#### 前言

> 请确保您的设备中已经安装完成`npm`和`node`环境，可在控制台进行验证，以下步骤默认您已经安装成功。

#### 安装`vue-cli`

```
  npm install vue-cli -g
```
> 注：为什么是全局？`vue-cli`的[工作原理](https://segmentfault.com/a/1190000009803941)是什么呢？

#### 使用`webpack`“打包机”

```
  # 当不填写project-name时便在当前目录下创建
  vue init webpack your-project-name
```

#### 安装模块到`node_modules`目录

```
  # 如果填写了project-name需要先进入该文件
  npm install <packageName>
```
> [npm模块安装机制简介,离线安装模块方法](http://www.ruanyifeng.com/blog/2016/01/npm-install.html)    

#### 运行

```
  npm run dev
```

#### 预览

这个就不用说了吧~

> 注：如何开启服务后自动打开?[该文中](https://juejin.im/post/5a3cd588518825519408d151)有介绍。另外还可以直接修改文件config中的index.js，找到字段autoOpenBrowser，令其值为true即可。

> 到这里想必你已经看到你的第一个vue跑起来了，是不是很激动呢？来到了vue的大家族中，再来几篇文章吧：

[怎么样也少不了的vue-cli官网文档](https://github.com/vuejs/vue-cli)   
[vue-cli#2.0webpack配置解析](https://juejin.im/post/584e48b2ac502e006c74a120)   
[webpack的中文文档](https://webpack.docschina.org/)

有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~

