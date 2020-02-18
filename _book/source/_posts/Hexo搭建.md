---
title: Hexo框架搭建博客记录
tags: blog
---

#### 前言
---

本文以主题为`material`讲解。

#### 1. 安装Hexo

```
  npm install -g hexo-cli
```

#### 2. 创建一个叫blog的网站

```
  // 切入到想放入blog的文件中
  hexo init blog
```

#### 3. 进入blog目录，运行该服务

```
  cd blog
  hexo server
```

#### 4. 打开浏览器，输入`http://localhost:4000/`查看

#### 5. 修改主题和`blog`目录下的配置文件(下面直接称为：站点配置文件)

* 将自己选的主题文件放入`themes`目录下(我用的：[material](https://material.viosey.com/docs/#/start))
* 打开站点配置文件，找到`theme`字段，将值改为自己选的主题
* 将此配置文件中的一些基本信息修改为自己的信息，具体详情请见：[Hexo配置](https://hexo.io/zh-cn/docs/configuration.html)
  * `language`：将值设置成为你网站所需要的语言，支持的语言在文件`/material/languages`中。
  * `description`：网站描述。主要用于SEO，告诉搜索引擎关于你站点的简单描述。
  * `url`：网站网址
  * `root`：网站根目录。注意自己网站是存放在哪个目录下的。
  * `theme`：改为`material`

#### 6. 修改选中主题中的配置文件

* 首先，将配置文件`_config.template.yml`copy一份到当前目录，并更名为`_config.yml`。详情请见：[安装说明](https://material.viosey.com/docs/#/start)。
* 更改`_config.yml`配置文件中的基本信息：
  * `keywords`：网站关键词
  * `site_verification`：搜索引擎验证，具体做法请参见：[给Hexo搭建的博客添加百度和谷歌的搜索引擎验证](https://www.jianshu.com/p/1ae43e700c45)
  * `google`：谷歌Search Console验证
  * `baidu`：百度站长平台验证
  * `structured_data`：在页面的`head`中生成结构化数据。这项功能需要你完善地配置主题的站点配置文件和主题配置文件。
  * `comment\use`：主题方式(我用的`disqus`)
  * `comment\shortname`：disqus shortname。// 这个在disqus的哪里？？
  * `analytics\google_site_id`：自己的谷歌`analytics`ID。


注：向搜索引擎验证你对站点的所有权，用于向搜索引擎提交sitemap和管理站点被搜索引擎收录的情况。

#### 7. 修改站点配置文件，将网站自部署到Github上

* 在文件的末尾加上：
```
  // 添加你要放博客的远程仓库部署
  deploy: type: git
  repo: https://github.com/github-name/github-name.github.io.git   // 只要是你仓库地址即可，没有强制要求.github.io仓库
  branch: master
```

#### 8. 安装部署使用的git插件

我们使用的是`git`源码管理工具，所以我们需要安装`git`包进行部署，安装这个插件才能使用`git`进行自动部署。

```
  npm install hexo-deployer-git --save
```

#### 9. 生成网站

部署网站前，我们需要先将其生成静态网站。进入`blog`目录执行下面命令，生成的网页将会存放在根目录自动生成的`public`文件夹中。

```
  hexo g
```

#### 10. 重启生成网站，进行自动部署网站

注：每次修改后都需要重新生成网站并进行部署。

```
  hexo d
```

---
参考文章：  
1. [Hexo简述](https://hexo.io/zh-cn/docs/index.html)  
2. [material用法](https://material.viosey.com/docs/#/start)  
3. [Hexo用法](https://blog.csdn.net/u012028371/article/details/78666998)  

有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~
