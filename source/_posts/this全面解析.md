---
title: this全面解析
tags: JavaScript
---

> 这是深入理解 JavaScript 语法合集的第六篇，其余文章见文末。
> 
> 文末介绍了一些辅助知识—— JavaScript 的词法作用域、动态作用域，以及 `this` 和 动态作用域的关系。如果这一部分还有疑问可以直接跳转到最后先阅读一下。

# 前言

在开始之前，我们要摆正两个观念：  
1. `this` 并不是指向本身  
2. `this` 在任何情况下，都不指向函数的词法作用域

那 `this` 到底什么呢？**this 的绑定和函数声明的位置没有任何关系，它是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里调用。换句话说，它只取决于函数的调用方式。**

# 全文大纲总览

* 绑定规则  
* 1. 默认绑定  
* 2. 隐式绑定  
* 3. 显示绑定  
* 4. `new` 绑定
* 优先级
* 软绑定
* 箭头函数的 `this` 指向
* 你可能不知道的词法作用域
* 1. 作用域
* 2. 词法作用域
* 3. 动态作用域

# 一、绑定规则

## （1）默认绑定

首先要介绍的是最常用的函数调用类型：独立函数调用。可以把这条规则当成是，当其它规则都无法应用时的默认选项。我们先来看一个例子：

```js
function foo() {
  console.log(this.a);
}

var a = 2;
foo(); // 2
```

我们可以看到，当我们调用 `foo()` 函数时，调用 `this.a`，`this` 解析了全局变量 `a` 。为什么呢？因为函数在调用时应用了 **默认绑定**，因此 `this` 指向了全局对象。

值得注意的是，如果函数使用严格模式，则不能将全局对象用于默认绑定，因此 `this` 会被绑定到 `undefined`。

```js
function foo() {
  "use strict";
  console.log(this.a);
}

var a = 2;
foo(); // TypeError: this is undefined
```

## （2）隐式绑定

根据调用位置是否有上下文对象判断是否为隐式绑定。

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo // 使用了es6的“简洁属性”
};

obj.foo(); // 2
```

从输出的结果中，我们可以判断：当函数引用上下文对象时，隐式绑定规则会把函数调用的 `this` 绑定到这个上下文对象。因为调用 `foo()` 时 `this` 被绑定到 `obj`，因此 `this.a` 和 `obj.a` 是一样的。

但是，并不是所有的事情都能随我们心愿的。在一些情况下，这个隐式绑定会丢失。我们来看一个例子：

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo
};

var bar = obj.foo; // 函数别名
var a = 4;

bar(); // 4
```
看！和我们想象中的不一样了。这是为什么呢？这是因为，虽然 `bar` 是 `obj.foo` 的一个引用，但是实际上，它引用的仅仅是 `foo` 函数本身，因此此时的 `bar()` 其实是一个不带任何修饰的函数调用，所以这里使用的规则变成了默认绑定。

另一种隐式丢失的情况是发生在参数传递的时候。当我们将函数作为参数传递时，该函数也会被隐式的赋值。

```js
function foo() {
  console.log(this.a);
}

function doFoo(fn){
  fn();
}

var obj = {
  a: 2,
  foo
};

var a = 4;
doFoo(obj.foo); // 4
```
## （3）显示绑定

在分析隐式绑定时，我们必须在一个对象内部包含一个指向函数的属性，并通过这个属性间接的引用函数，从而把 `this` 间接的绑定到这个对象上。

那么如果我们不想在对象内部包含函数引用，而想在某个对象上强制调用函数，该怎么做呢？

可以使用 `call()` 和 `apply()` 方法。这两个方法在 `this` 的应用是一致的，其它的区别请见：[apply、call的区别和用途](http://www.yaya12.com/archives/509)。

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2
};

foo.call(obj); // 2
```

可是，显示绑定仍然无法解决我们之前提到的绑定丢失的问题。下面有两种方法来解决这个问题。

**1.硬绑定**

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2
};

var bar = function() {
  foo.call(obj);
};

bar(); // 2
```

我们通过接用 `bar`，在其内部将 `foo` 的 `this` 强制绑定到 `obj` 上。这种方式就叫做硬绑定。

硬绑定比较有两个比较典型的应用场景，第一个是创建一个包裹函数，负责接受参数并返回值：

```js
function foo(something) {
  console.log(this.a, something);
  return this.a + something;
}

var obj = {
  a: 2
};

var bar = function() {
  return foo.apply(obj, arguments);
};

var b = bar(3); // 2 3
console.log(b); // 5
```

另一种是创建一个可以重复使用的辅助函数：

```js
function foo(something) {
  console.log(this.a, something);
  return this.a + something;
}

function bind(fn, obj) {
  return function() {
    return fn.apply(obj, arguments);
  };
}

var bar = bind(foo, obj);
var b = bar(3); // 2 3
console.log(b); // 5
```

由于硬绑定是一种非常常用的方式，所以ES5提供了内置的方法 `Function.prototype.bind`，它会返回一个硬编码的新函数，把指定的参数设置为 `this` 的上下文，并调用原始函数。`bind` 函数详解请看这里：[深度解析bind原理、使用场景及模拟实现](https://github.com/yygmind/blog/issues/23)

```js
function foo(something) {
  console.log(this.a, something);
  return this.a + something;
}

var obj = {
  a: 2
};

var bar = foo.bind(obj);

var b = bar(3); // 2 3
console.log(b); // 5
```

**2.API调用上下文**

许多的第三方库，以及 JavaScript 语言和宿主中许多新的内置函数，都提供了一个可选的参数，通常被称为“上下文”，其作用和 `bind()` 一样，确保你的回调函数使用指定的 `this`。

```js
function foo(el) {
  console.log(el, this.id);
}

var obj = {
  id: "1",
};

// 这里实际上就是通过call()或者apply()实现了显示绑定
[1, 2, 3].forEach(foo, obj);
// 1 "1"
// 2 "1"
// 3 "1"
```

## （4）`new` 绑定

在讨论 `new` 绑定之前，我们先来认清楚一件事情。在许多语言中(如java)，使用 `new` 初始化类时会调用类中的构造函数。那 JavaScript 是这样的嘛？我们来看看 `Number()` 作为构造函数时的行为，ES5.1 是这样描述的：  
> 15.7.2 Number 构造函数  
>
> 当 `Number` 在 `new` 表达式中被调用时，它是一个构造函数：它会初始化新创建的对象。

现在我们可以重新来定义一下 JavaScript 中的“构造函数”了：在 JavaScript 中，构造函数只是一些使用 `new` 操作符时被调用的函数。它不会属于某个类，也不会实例化一个类。实际上，它们只是被 `new` 操作符调用的普通函数而已。

当我们使用 `new` 操作符时，会自动执行以下四个操作：

1. 创建一个全新的对象  
2. 这个新对象会被执行 `[[Prototype]]` 连接  
3. 这个新对象会绑定到函数调用的 `this`  
4. 如果函数没有返回其它对象，那么 `new` 表达式中的函数会自动返回这个新对象。  

```js
function foo(a) {
  this.a = a;
}

var bar = new foo(2);
console.log(bar.a); // 2
```

# 二、优先级

先说结论：按照上面讨论的顺序，默认绑定 < 隐式绑定 < 显示绑定 < `new` 绑定。

不用说，默认绑定的优先级肯定是最低的。下面我们来对其它情况测试一下：

## （1）隐式绑定和显示绑定
```js
function foo() {
  console.log(this.a);
}

var obj1 = {
  a: 2,
  foo
};

var obj2 = {
  a: 3,
  foo
};

obj1.foo(); // 2
obj1.foo.call(obj2); // 3
```
我们看到，先将 `this` 绑定到对象 `obj1` 上，输出 `2`。当我们再将 `this` 显示的绑定给对象 `obj2` 时，输出了 `3`。  

## （2）隐式绑定和 `new` 绑定
```js
function foo(something) {
  this.a = something;
  console.log(this.a);
}

var obj = {
  foo
};

obj.foo(2); // 2
var b = new obj.foo(4); // 4
```

## （3）显示绑定和 `new` 绑定

我们前面在讨论 `bind` 函数时了解到，这个函数会忽略当前的 `this` 绑定，并把我们提供的对象绑定到对象上。这样看，好像硬绑定(显示绑定的一种)会比 `new` 的优先级更高一些，我们来看看是不是这样：

```js
function foo(something) {
  this.a = something;
}

var obj = {};

var bar = foo.bind(obj);
bar(2);
console.log(obj.a); // 2

var baz = new bar(3);
console.log(obj.a); // 2
console.log(baz.a); // 3
```

`bar` 被硬绑定到了 `obj` 上，但是 `new bar(3)` 并没有我们预计的那样把 `obj.a` 修改为 `3`。相反，`new` 修改了硬绑定(到 `obj` 的)调用 `bar()` 中的 `this`。

# 三、软绑定

前面提到，硬绑定会强制的将 `this` 绑定到指定的对象（除了 `new`），防止函数调用默认绑定规则。但是，这样做会大大的降低函数的灵活性，使用硬绑定之后就没有办法使用隐式绑定或显示绑定来修改 `this`。

如果可以给默认绑定指定一个全局对象和 `undefined` 以外的值，那就可以实现和硬绑定一样的效果，同时保留隐式绑定或显示绑定修改 `this` 的能力。

可以通过一种被称为软绑定的方法来实现我们想要的效果：

```js
if(!Function.prototype.softBind) {
  Function.prototype.softBind = function(obj) {
    var fn = this;
    var curried = [].slice.call(arguments, 1);
    var bound = function() {
      return fn.apply(
        (!this || this === (window || global)) ?
        obj : this,
        curried.concat.apply(curried, arguments)
      );
    };

    bound.prototype = Object.create(fn.prototype);
    return bound;
  };
}

function foo() {
  console.log("name:" + this.name);
}

var obj = {name: "obj"},
    obj2 = {name: "obj2"},
    obj3 = {name: "obj3"};

var fooOBJ = foo.softBind(obj);
fooOBJ(); // name: obj

obj2.foo = foo.softBind(obj); // 将obj传入，作为默认值，若this指向全局对象或undefined，则默认绑定obj
obj2.foo(); // name: obj2

fooOBJ.call(obj3); // name: obj3

setTimeout(obj2.foo, 10); // name: obj
```

# 四、箭头函数的 `this` 指向

或许只有少部分人才会关注箭头函数的 `this` 指向，但是这确实 ES6 引入箭头函数的一个重要原因之一——解决开发过程中 `this` 随着上下文动态调整以至于难以判断的问题。箭头函数不使用我们前面提到的四种标准规则，而是根据外层(函数或者全局)作用域来决定 `this`。

```js
function foo() {
  return (a) => {
    console.log(this.a);
  };
}

var obj1 = {
  a: 2,
};

var obj2 = {
  a: 3,
};

var bar = foo.call(obj1);
bar.call(obj2); // 2,不是3
```

`foo()` 内部创建的箭头函数会捕获调用时 `foo()` 的 `this`。由于 `foo()` 的 `this` 绑定到 `obj1`，`bar` 的 `this` 也会绑定到 `obj1`，箭头函数的绑定无法被修改，`new` 也不行。

# 你可能不知道的词法作用域


## （1）作用域

你可能在文章的最一开始就对“词法作用域”产生了疑惑，这是什么？

《你不知道的 JavaScript 》上卷第一章第一节介绍作用域时这样写道：
> 但是将变量引入程序会引起几个有意思的问题，也正是我们将要讨论的：这些变量住在哪里？换句话说，它们存储在哪里？最重要的是，程序需要时如何找到它们？
>
> 这些问题说明需要一套良好的规则来存储变量，并且之后可以方便的找到这些变量。这套规则被称为*作用域*。

作用域共有两种主要的工作模型。第一种是最为普遍的，被大多数编程语言所采作用的*词法作用域*，即静态作用域，还有一种叫做*动态作用域*。

JavaScript 所采用的就是前者，词法作用域。

## （2）词法作用域（静态作用域）

那什么是词法作用域呢？它的定义是这样的：词法作用域就是定义在词法阶段的作用域。

换句话说，**词法作用域是基于作用域嵌套的**，它在我们写代码或者定义时就确定的。比起动态作用域，它更关注我们的代码在何处声明。我们来看一个经典的栗子：
```js
let a = 3;

function bar() {
  console.log(a);
}

function foo() {
  let a = 4;
  bar();
}

foo();  // 3
```
看到没？最后输出的是全局作用域中的 `3` ，而不是调用它的 `foo` 函数中的 `4`。它的原理就是，当我们在函数 `bar` 中打印变量 `a` ，但是 `bar` 这个函数作用域中并没有这个变量，于是引擎就会去该构造函数作用域的外层作用域——全局作用域来查找，于是找到了变量 `a` 输出。

## （3）动态作用域

那动态作用域是如何表现的呢？我们来看看相同的栗子（使用 JavaScript 代码，仅仅是展示效果）：

```js
let a = 3;

function bar() {
  console.log(a);
}

function foo() {
  let a = 4;
  bar();
}

foo();  // 4
```
如果是动态作用域，那当我们调用 `foo()` 时，它会输出 `4`。原因是因为 **动态作用域中，当我们在当前作用域找不到该变量时，就会沿着调用栈向上查找**。引擎在函数 `bar` 中找不到变量 `a`，就会从沿着调用栈向上查找，找到了 `foo` 函数作用域中，这里定义了 `a`，于是最后返回了 `4`。

> 可能你也发现了，`this` 的隐式绑定和动态作用域的查找有些相似。它们都是基于调用栈来进行的。

最后，我们再总结一下：词法作用域是在写代码或者说是定义的时候确定的，而动态作用域是在运行时确定的（`this` 也是）。词法作用域关注函数在何处声明，而动态作用域关注函数从何处调用。

---
这是深入理解 JavaScript 语法合集的其中一篇，其它合集：  
[1. 深入理解 JavaScript —— 从设计一门语言讲起](https://syt-honey.github.io/2020/03/03/JavaScript%E8%AF%AD%E6%B3%95%E4%BB%8E%E5%A4%B4%E5%BC%80%E5%A7%8B/)  
[2. 你真的掌握了 JavaScript 变量和类型嘛？（上）](https://syt-honey.github.io/2020/03/06/%E5%8F%98%E9%87%8F%E5%92%8C%E7%B1%BB%E5%9E%8B%EF%BC%88%E4%B8%8A%EF%BC%89/)    
[3. 你真的掌握了 JavaScript 变量和类型嘛？（下）](https://syt-honey.github.io/2020/03/07/%E5%8F%98%E9%87%8F%E5%92%8C%E7%B1%BB%E5%9E%8B%EF%BC%88%E4%B8%8B%EF%BC%89/)  
[4. JavaScript 的原型和原型链](https://syt-honey.github.io/2020/03/12/%E5%8E%9F%E5%9E%8B%E5%92%8C%E5%8E%9F%E5%9E%8B%E9%93%BE/)  
[5. JavaScript 中的对象](https://syt-honey.github.io/2020/03/13/JavaScript%E4%B8%AD%E7%9A%84%E5%AF%B9%E8%B1%A1/)  

参考文章：  
1.《你不知道的 JavaScript 》上卷 by Kyle Simpson  
[2. JavaScript深入之词法作用域和动态作用域-2017-04-23](https://github.com/mqyqingfeng/Blog/issues/3) by mqyqingfeng   

有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~
