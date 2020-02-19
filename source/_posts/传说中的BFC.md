---
title: 今天来聊一聊传说中的BFC
tags: HTML&CSS
---

## BFC（Block Formatting Context）

前一章我们了解了语义化HTML结构的重要性，也清楚了在写网页结构时如何去写，现在我们来看看HTML5和CSS中一个非常重要的知识点——BFC。

### (1). BFC

BFC（Block Formatting Context），顾名思义，块格式化上下文。在讲它之前，我们先来看看下面两点：

**首先，什么是盒子？什么又是块盒子？**

CSS有一个概念，叫CSS视觉格式化模型（visiual formatting model）。它是用来处理和在视觉媒体上显示文档时使用的计算规则。它会根据CSS盒子模型将文档中的元素转换为一个个盒子。也就是说，它定义了盒子的生成。盒子主要包括了块盒、行内盒、匿名盒以及一些实验性的盒子，盒子的类型由 `display` 来决定。

块盒子（Block Box），前面说了，盒子的类型由属性 `display` 来决定。当元素的 `display` 为 `block` 、 `list-item` 或 `table` 时，这个元素就将是块级元素。一个块级元素会被格式化成一个块，默认按照垂直方向排列。每个块级元素都会生成至少一个块级盒子（即主块级盒子，principal block-level box），并且每个块级盒子都会参与块格式化上下文的创建。

我们说了块级盒子，那块级盒子和块盒子是啥关系？

我们先来看看另一个概念——块容器盒子。块容器盒子指的是该元素的直接子元素只有块级盒子或只有行内级盒子。

> A block container box either contains only block-level boxes or establishes an inline formatting context and thus contains only inline-level boxes.

如果还是理解起来有些费力，我们不妨来看张[图片](http://layout.imweb.io/article/box-type.html)：

![块容器盒子](/images/frontend/块容器盒子.png)

知道块容器盒子的概念后，我们再来说块盒子。块盒子就是块容器盒子的一个子集，即当块容器盒子内的元素全部都是块级盒子时，它就是块盒子。有点绕？其实我们可以简单的理解成，块盒子就是块容器盒子中的元素都是块级盒子。行内盒（Inline Box）& 匿名盒（Anonymous Block Boxes）详情请见：[盒类型](http://layout.imweb.io/article/box-type.html)

**其次，盒子的定位方案有哪些？**

在布局的时候，浏览器会根据元素的盒类型和上下文对这些元素进行定位。定位时有三种定位方案，分别是常规流，浮动以及绝对定位。

*常规流（Normal flow）*
* 在常规流中，盒子一个接一个的排列
* 在块级格式上下文中，它们在竖直方向上依次排列
* 在行内格式上下文中，它们在水平方向上依次排列
* 当 `position` 为 `static` 或 `relative`，并且 `float` 为 `none` 时会触发常规流
* 对于静态定位（默认定位，`position` 的值为 `static`），盒的位置是常规流布局。
* 对于相对定位（ `position` 的值为 `relative`），盒偏移的位置由 `top`，`bottom`，`left`，`right` 定义。不过，即便有偏移，仍然保留原有的位置。

*浮动（Float）*
* 盒子称为浮动盒

*绝对定位（Absolute positioning）*
* 绝对定位方案，盒子会从常规流中移除，不影响常规流的布局
* 它的定位相对于它的父元素
* 当元素的 `position` 的值为 `absolute` 或 `fixed`，它就是绝对定位元素
* 对于 `position: absolute`，元素的定位将相对于最近的一个 `ralative`、`fixed` 或 `absolute`的父元素，如果没有则相对于 `body`。

好了，现在我们讲回到 **块格式化上下文**。从上面的信息可以看出，块格式化上下文是页面CSS视觉渲染的一部分，用于决定块盒子的布局及浮动相互影响范围的一个区域。

### (2). 那BFC的创建方法有哪些呢？

1. 根元素 `<html>`
2. 浮动元素
3. 绝对定位元素，元素的 `position` 为 `absolute` 或 `fixed`
4. 行内块元素，`display: inline-block`
5. 表格单元格，`display: table-cell`
6. `overflow`值不为 `visible`的块元素
7. 弹性盒（flexbox），`display: flex` 或 `inline-flex`

### (3). BFC的范围

一个BFC包含创建该上下文元素的所有子元素，但不包括创建了新BFC中的任意元素。一句话概括就是，一个元素不能同时存在于两个BFC中。

BFC的一个重要的效果就是让处于BFC内部的元素与外部的元素相互隔离。可见，我们可以利用这个特定来防止浮动元素与其它元素重叠 —— 让重叠元素创建一个BFC，两个BFC之间互不干扰。

### (4). BFC的特性

1. BFC内部的盒会在竖直方向排列
2. 处于同一个BFC中的元素会相互影响，两个相邻盒的 `margin` 会发生叠加
3. BFC就是页面上的一个独立容器，容器内的元素不会受到外面元素的影响，反之亦然
4. 计算BFC的高度时，考虑BFC包含的所有元素，包括浮动元素的高度

根据BFC的这些特性，我们现在可以分析一下一些常见的问题：

**1.如何防止高度塌陷？**  
**2.如何防止BFC内部元素发生margin collapsing？**  

第一个问题是指，当我们对子元素设置浮动后，父元素高度发生塌陷现象。这是因为当我们为子元素设置浮动后，子元素创建了一个BFC，而其父元素没有创建BFC，因此父元素计算高度时并没有将浮动元素的高度算入，导致父元素的高度塌陷。根据特性，很容易想到解决方案之一就是让父元素也创建一个BFC。（特性4）

第二个问题是特性2的表现，解决方案之一就是为两个相邻的盒分别创建一个BFC，两个BFC相互之间不会影响。

---
这是HTML&CSS重点知识点合集的其中一篇，合集其它文章：  
[1.语义化HTML的重要性](https://syt-honey.github.io/2020/02/11/%E8%AF%AD%E4%B9%89%E5%8C%96HTML%E7%9A%84%E9%87%8D%E8%A6%81%E6%80%A7/)  

---
参考文章：  
[1.学习BFC-2017-09-12](https://juejin.im/post/59b73d5bf265da064618731d)  

有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~
