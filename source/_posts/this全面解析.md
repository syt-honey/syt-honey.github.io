---
title: this全面解析
tags: js
---

### 前言

在开始之前，我们要摆正两个观念：  
1.this并不是指向本身  
2.this在任何情况下，都不指向函数的词法作用域

那this到底什么呢？**this的绑定和函数声明的位置没有任何关系，它是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里调用。换句话说，它只取决于函数的调用方式。**

### 全文大纲总览

* 绑定规则  
  1.默认绑定  
  2.隐式绑定  
  3.显示绑定  
  4.new绑定
* 优先级
* 软绑定
* 箭头函数的this指向

### 一、绑定规则

#### 默认绑定

首先要介绍的是最常用的函数调用类型：独立函数调用。可以把这条规则当成是，当其它规则都无法应用时的默认选项。我们先来看一个例子：

```js
function foo() {
  console.log(this.a);
}

var a = 2;
foo(); // 2
```

我们可以看到，当我们调用foo()函数时，调用this.a，this解析了全局变量a。为什么呢？因为函数在调用时应用了**默认绑定**，因此this指向了全局对象。

值得注意的是，如果函数使用严格模式，则不能将全局对象用于默认绑定，因此this会被绑定到undefined。

```js
function foo() {
  "use strict";

  console.log(this.a);
}

var a = 2;
foo(); // TypeError: this is undefined
```

#### 隐式绑定

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

从输出的结果中，我们可以判断：当函数引用上下文对象时，隐式绑定规则会把函数调用的this绑定到这个上下文对象。因为调用foo()时this被绑定到obj，因此this.a和obj.a是一样的。

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
看！和我们想象中的不一样了。这是为什么呢？这是因为，虽然bar是obj.foo的一个引用，但是实际上，它引用的仅仅是foo函数本身，因此此时的bar()其实是一个不带任何修饰的函数调用，所以这里使用的规则变成了默认绑定。

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
#### 显示绑定

在分析隐式绑定时，我们必须在一个对象内部包含一个指向函数的属性，并通过这个属性间接的引用函数，从而把this间接的绑定到这个对象上。

那么如果我们不想在对象内部包含函数引用，而想在某个对象上强制调用函数，该怎么做呢？

可以使用call()和apply()方法。这两个方法在this的应用是一致的，其它的区别请见：[apply、call的区别和用途](http://www.yaya12.com/archives/509)。

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

1.硬绑定

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

我们通过接用bar，在其内部将foo的this强制绑定到obj上。这种方式就叫做硬绑定。

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

由于硬绑定是一种非常常用的方式，所以ES5提供了内置的方法`Function.prototype.bind`，它会返回一个硬编码的新函数，把指定的参数设置为this的上下文，并调用原始函数。bind函数详解请看这里：[深度解析bind原理、使用场景及模拟实现](https://github.com/yygmind/blog/issues/23)

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

2.API调用上下文

许多的第三方库，以及JS语言和宿主中许多新的内置函数，都提供了一个可选的参数，通常被称为“上下文”，其作用和bind()一样，确保你的回调函数使用指定的this。

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

#### new绑定

在讨论new绑定之前，我们先来认清楚一件事情。在许多语言中(如java)，使用new初始化类时会调用类中的构造函数。那JS是这样的嘛？我们来看看Number()作为构造函数时的行为，ES5.1是这样描述的：  
> 15.7.2 Number 构造函数  
当Number在new表达式中被调用时，它是一个构造函数：它会初始化新创建的对象。

现在我们可以重新来定义一下JS中的“构造函数”了：在JS中，构造函数只是一些使用new操作符时被调用的函数。它不会属于某个类，也不会实例化一个类。实际上，它们只是被new操作符调用的普通函数而已。

当我们使用new操作符时，会自动执行以下四个操作：

1.创建一个全新的对象  
2.这个新对象会被执行[[Prototype]]连接  
3.这个新对象会绑定到函数调用的this  
4.如果函数没有返回其它对象，那么new表达式中的函数会自动返回这个新对象。  

```js
function foo(a) {
  this.a = a;
}

var bar = new foo(2);
console.log(bar.a); // 2
```

### 二、优先级

先说结论：按照上面讨论的顺序，默认绑定 < 隐式绑定 < 显示绑定 < new绑定。

不用说，默认绑定的优先级肯定是最低的。下面我们来对其它情况测试一下：

1.隐式绑定和显示绑定

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
我们看到，先将this绑定到对象obj1上，输出2。当我们再将this显示的绑定给对象obj2时，输出了3。

2.隐式绑定和new绑定

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

3.显示绑定和new绑定

我们前面在讨论bind函数时了解到，这个函数会忽略当前的this绑定，并把我们提供的对象绑定到对象上。这样看，好像硬绑定(显示绑定的一种)会比new的优先级更高一些，我们来看看是不是这样：

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

bar被硬绑定到了obj上，但是`new bar(3)`并没有我们预计的那样把obj.a修改为3。相反，new修改了硬绑定(到obj的)调用bar()中的this。

### 三、软绑定

前面提到，硬绑定会强制的将this绑定到指定的对象(除了new)，防止函数调用默认绑定规则。但是，这样做会大大的降低函数的灵活性，使用硬绑定之后就没有办法使用隐式绑定或显示绑定来修改this。

如果可以给默认绑定指定一个全局对象和undefined以外的值，那就可以实现和硬绑定一样的效果，同时保留隐式绑定或显示绑定修改this的能力。

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

### 四、箭头函数的this指向

或许只有少部分人才会关注箭头函数的this指向，但是这确实ES6引入箭头函数的一个重要原因之一——解决开发过程中this随着上下文动态调整以至于难以判断的问题。箭头函数不使用我们前面提到的四种标准规则，而是根据外层(函数或者全局)作用域来决定this。

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

foo()内部创建的箭头函数会捕获调用时foo()的this。由于foo()的this绑定到obj1，bar的this也会绑定到obj1，箭头函数的绑定无法被修改，new也不行。

以上内容来自《你不知道的js》上卷。有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~
