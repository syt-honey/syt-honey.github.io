---
title: CSS三栏布局实现（实践篇）
tags: HTML&CSS
---

> 这是HTML&CSS重点知识点合集第五篇。其余文章见文末。

# CSS三栏布局实现

![常见布局方式](/images/frontend/wayOfLayout.png)

*注：题图来自[CSS常见布局方式](https://juejin.im/post/599970f4518825243a78b9d5)*

常见的CSS布局方式有：文档流布局（Normal flow）、浮动布局、定位布局、伸缩盒布局（FlexBox）、网格布局（Grid）、居中、圣杯布局、双飞翼布局等。

关于前面三种布局都属于传统盒模型布局方式，[前面文章](https://syt-honey.github.io/2020/02/19/%E4%BC%B8%E7%BC%A9%E7%9B%92/)中也有讲过。大家比较熟悉，这里就不赘述了。

关于伸缩盒布局和网格布局前面总结的[第三篇](https://syt-honey.github.io/2020/02/19/%E4%BC%B8%E7%BC%A9%E7%9B%92/)，[第四篇](https://syt-honey.github.io/2020/02/21/%E7%BD%91%E6%A0%BC%E5%B8%83%E5%B1%80/)也已经提到，并且剩下的圣杯布局、双飞翼布局都是三栏布局的不同实现，所以这篇文章的目的是对三栏布局不同实现方法予以实现及原理讲解。

## （1） 三栏布局

三栏布局特征是，两边定宽，中间一栏自适应。

比如，我们常见的一个布局栗子：
```html
<div class="container">
  <header class="header">这是header</header>
  <aside class="aside-left">左边内容</aside>
  <aside class="aside-right">右边内容</aside>
  <main class="main">这是文章主要内容</main>
  <footer class="footer">这是footer</footer>
</div>
```
```css
* {
    margin: 0;
    padding: 0;
    font-size: 14px;
}

.container {
  min-height: 100vh;
  margin: 0 auto;
}

.header {
  width: 100%;
  height: 30px;
  text-align: center;
  line-height: 30px;
  background: paleturquoise;
}

.main {
  width: 100%;
  background: palevioletred;
  height: 200px;
  padding: 0 100px;
  box-sizing: border-box;
}

.aside-left {
  width: 100px;
  height: 200px;
  background: purple;
  text-align: center;
  float: left;
}

.aside-right {
  width: 100px;
  height: 200px;
  text-align: center;
  background: purple;
  float: right;
}

.footer {
  background: paleturquoise;
  text-align: center;
  height: 30px;
  line-height: 30px;
}
```
![普通三栏布局](/images/frontend/threeListsLayout1.png)

我们通过对两侧边栏分别进行 `float: left;`、`float: right;`，中间的栏设置 `width: 100%;` 来实现两侧定宽，中间‘自适应’这样一个布局。

**它的原理是：**当我们为两边的侧边栏同时设置 `float` 时，两边的侧边栏就会脱离原来的文档流，各形成一个 [BFC](https://juejin.im/post/5e4bdc9df265da5734023968) 且不保留原来的位置，又由于 `<main>` 的长度被设置为百分百，所以它会占满整个父元素长度。中间`<main>`容器的两边会被分别定位到左上角和右上角，分别与左边的 `<aside>` 和右边的 `aside` 发生重叠。如果这时 `<main>` 里面有内容，则里面的内容会受到两侧BFC的影响，将被挤到中间。这就是为什么虽然容器发生了重叠，但是内容可以看见。

## （2）特殊的三栏布局——让你最想渲染的部分先渲染

对于我们常说的布局——圣杯布局、双飞翼布局，它们不同于普通三栏布局的是，它们会将中间的`<main>`写在`<aside>`的前面，优先渲染主要的内容。

你可能已经注意到了，我们刚刚在写HTML时把 `<aside>` 放在了 `<main>` 的前面。但是一般情况下，我们希望像圣杯布局一样先渲染中间的主要内容。

先来看看，把 `<main>` 提到前面但不改变样式时布局是什么样的：
```html
<div class="container">
  <header class="header">这是header</header>
  <main class="main">这是文章主要内容这是文章主要内容这是文章主要内容这是文章主要内容这是文章主要内容这是文章主要内容这是文章主要内容这是文章主要内容这是文章主要内容这是文章主要内容</main>
  <aside class="aside-left">左边内容</aside>
  <aside class="aside-right">右边内容</aside>
  <footer class="footer">这是footer</footer>
</div>
```
![特殊三栏布局html](/images/frontend/threeListsLayout2.png)

两边的侧边栏脱离了文档流在分别定位到了下方的两侧（注意， `main` 的内容这时铺满了整个容器，这是因为这时BFC对它已经没有影响了）。现在我们需要做的就是将这两个BFC‘上提’到 `main` 的两侧。

一种方法是通过 `margin-top` 来进行‘上提’。这里我们使用另一种方法：对 `<main>` 进行绝对定位，让它生成自己的BFC，同时设置 `left` 和 `right` 的值给两边的侧边栏留位置。所以代码是这样的：

```css
.header {
  width: 100%;
  height: 30px;
  text-align: center;
  line-height: 30px;
  background: paleturquoise;
}

.main {
  background: palevioletred;
  height: 200px;
  position: absolute;
  left: 100px;
  right: 100px;
}

.aside-left {
  width: 100px;
  height: 200px;
  background: purple;
  text-align: center;
  float: left;
}

.aside-right {
  width: 100px;
  height: 200px;
  text-align: center;
  background: purple;
  float: right;
}

.footer {
  clear: both;
  background: paleturquoise;
  text-align: center;
  height: 30px;
  line-height: 30px;
}
```
效果也是一样的：
![特殊三栏布局](/images/frontend/threeListsLayout1.png)

明白了嘛？来[这里](https://codepen.io/syt-honey/pen/yqPvGv?__cf_chl_jschl_tk__=cbcccdf885dcb23526614bba32f369db8b2a9c2d-1583199551-0-AQH1c4Zo2KfcfiM0Itw3xKC_4aR4mFl8tnVB_FMuG7DwjCBTLd0Vy4nukrXE4UPWXLJW3HMzGhXnq04vVSZCxXvJAqrjR-DYrxSAp8kA04P3xZo-yutpn_lAzrpD01WE4nH1A6onxo_tTSlg2LsfJTfvfeqflzMb2I0sbC-nri8CbR0tBqj7Axe8grifpLJVYIujuOz8yZ4RXCvQ2aNF0kyeYIMF1QrAKVsYH3FoVIoXo_fsCeROwA0FooP50v_HIZCr1riG3RvH6hOm1rpLC_K_z5EkBMgqaegZ7DWv7ekTfJT5BTrS02CcTUidXl5oSJjwDT094dqtYsjKs4_BlccRHaFB9EbNIA9P38OFnXDB)实践一下吧！

> 我看到很多文章都写了圣杯布局和双飞翼布局。但是思考过后还是没有这样来写。
> 因为，我认为圣杯布局也好双飞翼布局也罢，它们的表现结构都是一样的目的也是相同的（都是将主要内容放在前面优先渲染），那就没必要去纠结是哪一种布局方式了。我们需要面对的问题是：对于这种特殊的三栏布局，我们有哪些实现方法呢？任何实现方式 ，只要能实现这个目的，我觉得都ok~（当然，你要知道为啥这种方式可以？你要知道它的原理才行。）


---
这是HTML&CSS重点知识点合集的其中一篇，合集其它文章：  
[1.语义化HTML的重要性](https://syt-honey.github.io/2020/02/11/%E8%AF%AD%E4%B9%89%E5%8C%96HTML%E7%9A%84%E9%87%8D%E8%A6%81%E6%80%A7/)  
[2.传说中的BFC](https://syt-honey.github.io/2020/02/18/%E4%BC%A0%E8%AF%B4%E4%B8%AD%E7%9A%84BFC/#more)  
[3.CSS布局神器——伸缩盒（语法篇）](https://syt-honey.github.io/2020/02/19/%E4%BC%B8%E7%BC%A9%E7%9B%92/)  
[4.网格布局 ——（语法篇）](https://syt-honey.github.io/2020/02/21/%E7%BD%91%E6%A0%BC%E5%B8%83%E5%B1%80/)  

---
参考文章：  
[1.CSS常见的布局方式-2017-08-20](https://juejin.im/post/599970f4518825243a78b9d5) by sunshine小小倩   

有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~
