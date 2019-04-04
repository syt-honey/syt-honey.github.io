---
title: gitbook的正确打开方式
tags: gitbook
---

背景：最近在参加了一个学习英语课程(阅读英文原著)，内容觉得还不错，想分享给我的小伙伴们，就想着写这个啦。
下面我就写写我的安装步骤，还走了蛮多坑的。

---

#### 1. 卸载掉之前安装的gitbook(如果没有安装就忽略这一步)

#### 2. 安装node.js
```
  // 测试
  npm --version
```

#### 3. 你可能会遇到这样的问题：
![nodejsRep](/images/gitbook/nodejsRep.png)

解决方法：指定node.js的仓库为国内最大的node.js社区`http://cnodejs.org`提供的仓库，这里有几种方式：
* 通过config命令
```
  npm config set registry http://registry.cnpmjs.org
  npm info underscore
```
* 在npm命令行指定
```
  npm --registry http://registry.cnpmjs.org info underscore
```
* 修改 ~/.npmrc
```
  registry = http://registry.cnpmjs.org
```
其中，第三个是只需要设置一次即可。(不过我还是用了第二种方法，效果立竿见影啊~)
![success-nodejsRep](/images/gitbook/success-nodejsRep.png)

**注：**几小时之后更：还是乖乖用了第三个。。。每回都要写一次，认怂……
#### 4. 安装gitbook
```
  npm install gitbook-cli -g
  // 检测
  gitbook -V
```

#### 5. 初始化目录列表
```
  gitbook init
```

#### 6. 新建book.json文件，下面贴出我的文件：
```json
{
  "output": null,
  "generator": "site",
  "author": "Honeysyt",
  "title": "How to Win Friends and Inflence People",
  "description": "This is an original book of Dale Carnegie, which named 'How to Win Friends and Influence People'.",
  "extension": null,
  "plugins": [
    "include-codeblock",
    "ace"
  ],
  "template": "ace",
  "unindent": true,
  "edit": true,
  "pluginsConfig": {},
  "variables": {},
  "links": {
    "sidebar": {
      "Have problems? Issues, give it to me~": "https://github.com/syt-honey/How-to-Win-Friends-and-Influence-People/issues"
    },
    "sharing": {
      "google": null,
      "facebook": null,
      "twitter": null,
      "weibo": null,
      "all": null
    }
  },
  "pdf": {
    "pageNumbers": false,
    "fontSize": 12,
    "paperSize": "a4",
    "margin": {
      "right": 62,
      "left": 62,
      "top": 36,
      "bottom": 36
    },
    "headerTemplate": null,
    "footerTemplate": null
  }
}
```
你可以根据你自己的情况来改。

#### 7. 好啦，前面这些部分大概就是这样。后续步骤很多文章都有写，我就不写啦。遇见什么问题，欢迎和我交流~

有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~
