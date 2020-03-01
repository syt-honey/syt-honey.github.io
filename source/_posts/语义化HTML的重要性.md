---
title: 语义化HTML的重要性
tags: HTML&CSS
---

> 牛客的专向题刷完了，系统的总结一下考点，争取搞清楚各种知识点的来龙去脉。前端精进之路，新的一年，冲鸭！这是HTML&CSS重点知识点合集第一篇。其余文章见文末。

## 一、HTML

不管是不是做前端的，相信大家都听说过HTML并且自己随随便便就能看着几个现成的网站样式写出几行代码。但是，你有考虑过你真的把HTML用好了嘛？相信你也知道HTML控制的是整个网页的结构，并且它是语义化的。那你在写代码的时候真的把结构语义化考虑进去了嘛？现在写前端代码都流行一个div走遍天下，实现一个导航栏是一个div走到底，实现一个表单也是一个div走到底，你也是这样的嘛？

## 二、HTML语义化真的有说的那么重要嘛？

首先需要说明的是，其实HTML本身是没有表现的。我们看到的，比如 `<h1>` 是粗体，字体大小为2em；`<strong>` 加粗，这其实是HTML默认的CSS样式在起作用。所以我们首先要知道，HTML和页面的表现是没有关系的，这些是CSS的事情。HTML在页面中的作用就是结构和含义。

现在我们来讨论一下，语义化的HTML结构到底有什么好处。

1.在样式丢失时也能让页面呈现清晰的结构；
2.更好的支持各种终端，屏幕阅读器会完全根据你的标记来“读取”网页的内容。例如无障碍阅读和有声小说等；
3.利于搜索引擎的抓取。搜索引擎的爬虫也依赖于标记来确定上下文和各个关键字的权重。
4.便于团队开发和维护。

对于以上这四点，在日常用到的软件或网站中许多都有体现。当你进入谷歌邮箱时，假设这时你的网络不好，你可以直接加载进入“基本HTML”的简略版本。再比如，我们日常开发中，很多人都喜欢用div去实现一个button，认为这样不仅添加样式方便还可以不用管浏览器的兼容性问题。但是我们很容易忽视的一点是，像div、span这样的标签在默认情况下是没有办法自动获得焦点的（当我们按tab键时会自动跳过），这对特殊人群无疑是没有任何帮助的。

我们再来看一个例子，假如我们写了一个表单，提交表单时没有用到 `input type="submit"`，取而代之的是使用 `a` 标签的 `click` 事件做提交。一般情况下是可以正常工作的，但是如果这时候Javascript被禁用了怎么办？回过头来，如果我们是用的如下文档结构，即便在Javascript被禁用的情况下，表单仍然可以提交。

```Javascript
<form>
  <input type="text">
  <input type="submit">
</form>
```

代码实现[优雅降级](https://juejin.im/post/58eda95261ff4b005819faf9)也需要正确的体现HTML标签的语义，这样符合浏览器的预期。让我们网页在各种情况下，如Javascript被禁用，CSS传输失败等等，都可以运作良好。

同时，语义化的HTML结构不仅仅给我们开发人员看的，更是给搜索引擎看的。

比如我们一个页面可以这样写：
```html
<div class="body">
  <div class="nav">
    <div class="nav-item">Home</div>
    <div class="nav-item">My Articles</div>
  </div>
  <div class="sidemenu">
    <div class="sidemenu-item">Like</div>
    <div class="sidemenu-item">Share</div>
    <div class="sidemenu-item">Add to favorite</div>
  </div>
  <div class="container">
    <div class="heading">This is the heading</div>
    <div class="content">
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Rerum, consequuntur!
    </div>
    <div class="button">Next Page</div>
  </div>
  <div class="footer">
    copyright@2020
  </div>
</div>
```

也可以这样写：
```html
<body>
  <nav>
    <ul>
      <li>Home</li>
      <li>My Articles</li>
    </ul>
  </nav>
  <aside>
    <ul>
      <li>Like</li>
      <li>Share</li>
      <li>Add to favorite</li>
    </ul>
  </aside>
  <section>
    <h3>This is the heading</h3>
    <article>
      Lorem ipsum dolor sit, amet consectetur adipisicing elit. Quidem, assumenda?
    </article>
    <button>Next Page</button>
  </section>
  <footer>
    copyright@2018
  </footer>
</body>
```

---
这是HTML&CSS重点知识点合集的其中一篇，合集其它文章：  
[2.传说中的BFC](https://syt-honey.github.io/2020/02/18/%E4%BC%A0%E8%AF%B4%E4%B8%AD%E7%9A%84BFC/#more)  
[3.CSS布局神器——伸缩盒（语法篇）](https://syt-honey.github.io/2020/02/19/%E4%BC%B8%E7%BC%A9%E7%9B%92/)

---
参考文章：  
[1.语义化的HTML结构到底有什么好处？-2009-07-10](https://www.html.cn/archives/1668)  
[2.为什么有些前端一直用 div 当按钮，而不是用 button？-2018-10-30](https://www.zhihu.com/question/296706208)  
[3.理解HTML语义化-2014-03-21](https://www.cnblogs.com/freeyiyi1993/p/3615179.html)  
[4.网格布局 ——（语法篇）](https://syt-honey.github.io/2020/02/21/%E7%BD%91%E6%A0%BC%E5%B8%83%E5%B1%80/#more)  

延展阅读：  
[1.前端不止：Web内容的无障碍性](https://zhuanlan.zhihu.com/p/37397635)  

有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~
