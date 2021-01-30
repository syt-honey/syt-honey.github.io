---
title: JavaScript 中的对象
tags: JavaScript
---

> 这是深入理解 JavaScript 语法合集的第五篇，其余文章见文末。

# 前言

在 JavaScript 的操作中，绝大部分都是对对象进行操作。关于你不知道的一些内容，今天我们就来讲解一下。希望你对 JavaScript 中对象的理解也能更加的深入。

## 一、语法

### （1）对象的创建

对象可以通过两种方式进行定义：声明形式和构造形式，即：
```js
// 声明形式
let firstObj = {
  key: value
};

// 构造形式
let secondObj = new Object();
secondObj.key = value;
```

声明形式和构造形式生成的对象是一样的。唯一的区别是，在文字声明中可以添加多个属性，而在构造形式中需要一个一个的添加。

## 二、类型

在具体讲对象之前，我们来深入的了解一下 [这篇文章](https://syt-honey.github.io/2020/03/07/%E5%8F%98%E9%87%8F%E5%92%8C%E7%B1%BB%E5%9E%8B%EF%BC%88%E4%B8%8B%EF%BC%89/) 中的对象类型。

我们在讲对象类型时介绍到了一些特殊的对象类型，我们还把其中的一部分类型称为了对象类型系统。并且还检测到对象系统中的所有类型使用 `typeof` 进行检测返回的都为 `object`。对象系统中的类型包括：`Object`、`Array`、`Date`、`RegExp`。

其实我们之前介绍的基本包装类型：`Number`、`String`、`Boolean` 也属于对象类型系统。不仅如此，还有 `Error`。

这些都是 JavaScript 中的对象类型，它们还有另一个名称：内置对象。

* `String`
* `Number`
* `Boolean`
* `Object`
* `Function`
* `Array`
* `RegExp`
* `Date`
* `Error`

可能你有些奇怪，不是说这些是对象类型嘛？怎么又变成内置对象了？其实这只是一种说法而已。实际上，它们只是一些内置函数。并且这些内置函数还可以看成是构造函数，然后 `new` 出一个对象来（任何函数前面加一个 `new` 都是构造函数:) ）。

不信我们可以试试看：
```js
let str = new String('123');

console.log(str);  // String {"123"}
```
![new String](/images/frontend/js/newString.png)

## 三、关于对象的内容

这一小结我们主要讲对象的属性和方法的相关知识，涉及属性名、属性访问以及属性遍历等知识点。

### （1）对象属性

#### 1. 属性访问

对象属性的访问一般有两种方式：`.` 和 `[]` 操作符。前者通常被称为“属性访问”，后者通常被称为“键访问”。
```js
let obj = {
  a: 2
};

obj.a;  // 2
obj["a"];  // 2
```
这两种方法访问到的内容都是相同的，所以这两种术语经常可以互换。

这两种语法的主要区别在于：`.` 操作符要求属性名符合标识符的命名规范，而 `["..."]` 语法可以接受任意 UTF-8/Unicode 字符串作为属性名。

需要注意，`["..."]` 操作符接收的是字符串。若输入内容不是字符串，那么会将输入的内容转换成字符串，数字、对象都不例外。
```js
let obj = {};

obj[true] = "one";
obj[3] = "two";
obj[obj] = "three";

obj["true"];  // one
obj["3"];   // two
obj["[object Object]"];  // three
```

#### 2. 可计算属性名

ES6 增加了“可计算属性名”，可以在文字中使用 `[]` 包裹一个表达式来当作属性名。它的用法是：`obj[prefix + name]`。
```js
let prefix = "one";

let obj = {
  [prefix + "bar"]: "this is the first",
  [prefix + "foo"]: "this is the second"
};

obj["onebar"];  // "this is the first"
obj["onefoo"];  // "this is the second"
```

可计算属性名最常用的场景就是使用 `Symbol` 当作对象的属性名。

> 由于 `Symbol` 的值是唯一的（并不绝对），所以当我们将其作为对象的属性名是就不需要担心属性名发生重合的问题了。
>
> 顺便提一句，对象中存储的键只能是 `Symbol` 或者字符串。

#### 3. 属性特性检测 —— `Object.getOwnPropertyDescriptor()`

在 ES5 之前，JavaScript 本身并没有可以直接检测属性特性的方法，比如判断属性是否可读。但从 ES5 开始，所有的属性都具备了属性描述符。这个特性使得我们可以方便的判断普通对象的属性是否可读。
```js
let obj = {
  a: 2
};

Object.getOwnPropertyDescriptor(obj, "a");
```

输出为：  
![getOwnPropertyDescriptor](/images/frontend/js/getOwnPropertyDescriptor.png)

经输出，我们可以看到，使用该方法不仅可以查看到对象 `obj` 中属性 `a` 的 `value`（值），我们还看到其它几个键：`writable`、`enumerable`、`configurable`。这几个键定义了当前属性的一些特性，比如是否可写？是否可枚举？（决定了当遍历对象属性时是否能遍历的到）以及是否可配置？这些特性直接决定了某些操作是否有用。

既然这些特性这么重要，那我们如何主动的去设置这些特性呢？

#### 4. 添加属性、修改已有属性—— `Object.defineProperty()`

**1. Writable**  
在创建普通属性时属性描述符会使用默认值。我们也可以使用 `Object.defineProperty()` 来添加一个新属性或者修改一个已有属性。
```js
let obj = {};

Object.defineProperty(obj, "a", {
  value: 2,
  writable: true,
  enumerable: true,
  configurable: true
});

obj.a;  // 2
```

我们为对象 `obj` 添加了一个名为 `a` 的新属性，且为其设置了三个特性。这和前面直接在对象中设置属性是一样的。

在设置的时候，我们也可以根据需求来更改这些特性。比如，你希望这个属性一旦设置之后不可以更改（可读不可写），那你可以将 `writable` 设置为 `false`：  
![definePropertyWritableFalse1](/images/frontend/js/definePropertyWritableFalse1.png)  
需要注意的是，前面提到的不可写仅仅是指在外部不可写。如果我们重新使用 `Object.defineProperty()` 方法，且对其中的 `value` 值进行更改，那该属性的值照样会被覆盖。
![definePropertyWritableFalse2](/images/frontend/js/definePropertyWritableFalse2.png)

**2. Configurable**  
那如果我们不希望它被覆盖掉怎么办？这时候特性 `configurable` 就派上用场了。它的作用就是决定当前属性是否可以进行重新配置。  
![definePropertyConfigurable1](/images/frontend/js/definePropertyConfigurable1.png)  
当我们对一个 `configurable` 值为 `false` 的属性重新使用 `Object.defineProperty()` 进行配置时就会抛出一个 `TypeError` 的异常。

`configurable` 修改成 `false` 是一个单向操作，设置之后就无法撤销。

> 还有一个小例外需要注意：即便当前属性的 `configurable` 为 `false`，我们还是可以把当前属性的 `writable` 的状态由 `true` 改为 `false`，但是反过来则不行。（`enumerable` 的值不能进行修改，会报错）

![definePropertyConfigurable2](/images/frontend/js/definePropertyConfigurable2.png)

**3. Enumerable**  
从名字就可以看出，该特性控制的是属性是否会出现在对象的属性枚举中，比如 `for...in` 循环。

用户定义的所有普通属性默认都是 `enumerable: true`，也就是说，这个属性会在你枚举的属性列表中出现。

#### 5. 对象内置操作 —— `[[Get]]`

你有没有思考过，当我们访问属性，它的原理是如何运作的？比如：
```js
let obj = {
  a: 2
};

obj.a; // 2
```

最后的一段代码是一次属性访问，但是这条语句不仅仅是在 `obj` 中查找名字为 `a` 的属性。

在语言规范中，`obj.a` 在 `obj` 上实际上是实现了 `[[Get]]` 操作。对象默认的内置 `[[Get]]` 操作首先在对象中查找是否有名称相同的属性，如果找到就返回这个属性的值。

如果没有找到名称相同的属性，该操作将会沿着原型链进行查找，若找到则返回，若没有则返回 `undefined`。
```js
let obj = {
  a: 2,
  d: undefined
};

Object.defineProperty(Object.prototype, "b", {
    value: 3,
});

obj.a;  // 2
obj.b;  // 3
obj.c;  // undefined
obj.d;  // undefined
```

如上代码所示，总共分为三种形式：
* 属性可以在对象中找到
* 属性在对象中找不到，但是在它的原型链上可以找到
* 属性在对象和原型链中均找不到

第一种情况：属性 `a` 和 `d` 可以在对象中直接找到，`[[Get]]` 操作直接返回其值。  
第二种情况：属性 `b` 在对象中找不到，但是可以在它的原型对象（也就是原型链上）中找到，返回其值。  
第三种情况，对于属性 `c` 我们在对象和原型链中均找不到，返回 `undefined`。  

也许你注意到了，属性 `c` 和属性 `d` 均返回了 `undefined`，而前者并没有定义。

我们可以以此得出一个结论：仅通过返回值，是无法判断一个属性是否存在并且持有一个 `undefined` 值。

#### 6. 对象内置操作 —— `[[Put]]`

当我们读取对象属性时会自动调用对象默认的内置操作 `[[Get]]` 。相似的，当我们为对象属性赋值时也会调用内置操作 `[[Put]]`。

`[[Put]]` 被触发时，实际的行为取决于许多因素，其中一个很重要的因素就是：当前对象是否含有这个属性。

假设我们执行 `obj.b = 4;`：

如果对象 `obj` 中已经包含属性 `b`，`[[Put]]` 算法会检查一下内容：
1. 该属性是否是访问描述符（访问的属性是否是 `getter`）？如果是并且存在 `setter` 就调用 `setter`。
2. 该属性的数据描述符中 `writable` 是否为 `false`？如果是，在非严格模式下，修改失效，在严格模式下抛出 `TypeError` 异常。
3. 如果都不是，将该值设置为属性 `b` 的值。

如果属性 `b` 不直接存在于这个对象中，而是存在于原型链上层时，会有三种情况：
1. 如果原型链上层存在属性 `b`，且其特性 `writable` 的值为 `true`，那么就会直接在 `obj` 中添加一个名为 `b` 的新属性。
2. 如果原型链上层存在属性 `b`，且其特性 `writable` 的值为 `false`，那么将无法修改已有的属性且无法在对象 `obj` 上创建该属性。如果运行在严格模式下，代码会抛出一个错误。否则，这条语句被忽略。
3. 如果在原型链上层存在属性 `b`，并且属性 `b` 是一个 `setter`，那将调用这个 `setter`。属性 `b` 不会被添加到对象 `obj` 中，且原型链中的 `setter` 也不会被重新定义。

可能有些难理解，尤其是第二条。只读属性会阻止原型链下层隐式创建同名属性。其实这样做主要是为了模拟类属性的继承。我们可以这样思考，在 JavaScript 中，原型链这个特性常被用来实现“继承”。从这个角度来看，原型链上层的属性将被下层的属性“继承”，那如果上层的属性被设置为不可写，那自然“继承”下来的属性也是不可改变的（第二条）。如果可写，那也将会自动的生成该属性（第一条）。但是一定要注意，实际上并不会发生类似的继承赋值。

> 对于第二条，它只存在于 `=` 赋值中，如果我们使用 `defineProperty()` 并不会受影响。

![PutPropertyWritableFalse](/images/frontend/js/PutPropertyWritableFalse.png)

#### 7. 对象的隐藏函数 —— `Getter`、`Setter`

对象默认的 `[[Put]]` 和 `[[Get]]` 操作分别可以控制属性值的控制和获取。

ES5 中可以使用 `getter` 和 `setter` 改写默认操作。当我们为属性定义 `getter` 、`setter`或者同时定义时，这个属性会被定义为 “访问描述符”（刚刚提到过）。对于访问描述符来说，JavaScript 会忽略它们的 `value` 和 `writable` 特性，取而代之的是关心 `set` 和 `get` （还有 `configurable`、`enumerable`）。
```js
let obj = {
  get a() {
    return 2;
  }
};

Object.defineProperty(obj, "b", {
  get() {
    return this.a*2;
  }
});

obj.a;  //  2
obj.b;  // 4
```

不管是对象文字语法中的 `get a() {..}` ，还是 `defineProperty(..)` 中的显示定义，二者都会在对象中创建一个不包含值的属性，对于这个属性的访问会自动调用一个隐藏的函数，它的返回值会被当作属性访问的返回值。

为了让属性更合理，还应当定义 `setter`。`setter` 会覆盖单个属性默认的 `[[Put]]` 操作。通常来说，`getter`、`setter` 是成对出现的：
```js
let obj = {
  get a() {
    return this._a_;
  }

  set a(val) {
    this._a_ = val * 2;
  }
};

obj.a = 2;
obj.a; // 4
```

对于上面这段代码，`obj.a` 的赋值操作调用了 `set` 方法，使 `_a_` 的值变为 `4`。当我们再去输出 `obj.a` 时调用 `get` 方法输出 `4`。

#### 8. 遍历

你肯定早就用过 `for...in` 循环来遍历属性了。`for...in` 循环可以用来遍历对象的可枚举属性列表（包括原型链）。遍历属性名的有了，那我们如果想要遍历属性值应该怎么办？

如果是遍历数组，那么我们可使用普通的 `for` 循环，然后利用下标来访问值。但是这本质上还是使用“属性名”来访问它的值。

那么如何直接遍历值而不是数组下标（或者对象属性）呢？ES6 添加了遍历数组的 `for...of` 循环语法：
```js
let arr = [1, 2, 3];

for(let v of arr) {
  console.log(v);
}
// 1
// 2
// 3
```

`for...of` 循环首先会向被访问对象请求一个迭代器对象，然后通过调用迭代器对象的 `next()` 方法来遍历所有返回值。

数组有内置的 `@@iterator` ，因此 `for...of` 可以直接应用在数组上。我们使用内置的 `@@iterator` 来手动遍历数组：
```js
let arr = [1, 2, 3];
let it = arr[Symbol.iterator]();

it.next();  // { value: 1, done: false }
it.next();  // { value: 2, done: false }
it.next();  // { value: 3, done: false }
it.next();  // { done: true }
```

> 我们使用 ES6 中的符号 `Symbol.iterator` 来获取对象的 `@@iterator` 内部属性。引用类似 `iterator` 的特殊属性时要使用符号名，而不是符号包含的值。此外， `@@iterator` 本身并不是一个迭代器对象，而是一个返回迭代器对象的函数。

和数组不同，普通的对象没有内置的 `@@iterator` ，所以无法自动完成 `for...of` 遍历。当然，我们也可以给任何想遍历的对象定义 `@@iterator`：
```js
let obj = {
  a: 2, 
  b: 3
};

Object.defineProperty(obj, Symbol.iterator, {
  enumerable: false,
  writable: false,
  configurable: true,
  value() {
    let o = this;
    let idx = 0;
    let ks = Object.keys(o);
    return {
      next() {
        return {
          value: o[ks[idx++]],
          done: (idx > ks.length)
        };
      }
    };
  }
});

// 手动遍历 obj
let it = obj[Symbol.iterator]();

it.next(); // { value:2, done: false }
it.next(); // { value:3, done: false }
it.next(); // { value:undefined, done: true }

// 使用 for...of 遍历
for(let v of obj) {
  console.log(v);
}
// 2
// 3
```

注意，这里我们使用了 `Object.defineProperty` 来定义属性 `[Symbol.iterator]`。我们主要的目的是让它不可枚举。还有，这里我们使用了符号，我们也可以直接在定义对象时进行声明，比如：
```js
let obj = {
  a: 2, 
  b: 3,
  [Symbol.iterator]() {
    //
  }
};
```

> 其中 `obj[Symbol.iterator]()` 是访问对象中的 `Symbol.iterator` 函数。

## 四、总结

这篇文章中，我们主要讲了访问对象的方式、访问属性的过程中发生了什么？它的原理是怎样的？当我们设置属性值时它的原理又是怎样的？以及对属性描述符、访问描述符有了一个具象的了解。我们还讲了重要的 `setter` 和 `getter`，这两个隐藏方法的一个著名的实践就是前端框架 `vue` 中的双向绑定。最后，我们讲了属性的遍历，分别讲了属性名的遍历和属性值的遍历。

---
这是深入理解 JavaScript 语法合集的其中一篇，其它合集：  
[1. 深入理解 JavaScript —— 从设计一门语言讲起](https://syt-honey.github.io/2020/03/03/JavaScript%E8%AF%AD%E6%B3%95%E4%BB%8E%E5%A4%B4%E5%BC%80%E5%A7%8B/)  
[2. 你真的掌握了 JavaScript 变量和类型嘛？（上）](https://syt-honey.github.io/2020/03/06/%E5%8F%98%E9%87%8F%E5%92%8C%E7%B1%BB%E5%9E%8B%EF%BC%88%E4%B8%8A%EF%BC%89/)  
[3. 你真的掌握了 JavaScript 变量和类型嘛？（下）](https://syt-honey.github.io/2020/03/07/%E5%8F%98%E9%87%8F%E5%92%8C%E7%B1%BB%E5%9E%8B%EF%BC%88%E4%B8%8B%EF%BC%89/)  
[4. JavaScript 的原型和原型链](https://syt-honey.github.io/2020/03/12/%E5%8E%9F%E5%9E%8B%E5%92%8C%E5%8E%9F%E5%9E%8B%E9%93%BE/)  


参考文章：  
1.《你不知道的 JavaScript》上卷 by Kyle Simpson

有问题？发送 [issues](https://syt-honey.github.io/about/) 给我~
