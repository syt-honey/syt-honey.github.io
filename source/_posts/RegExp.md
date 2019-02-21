---
title: RegExp
tags: js 
---

### 前言
---
>前言：(等等，前言？怎么又是前言？哈哈，每回记录都是有背景的嘛。)写这篇文章是因为想系统的记录一下js中正则表达式的用法，以后忘记了也方便查询。还有一件值得一提的事，和别的地方一样`[]`括起来的表示该项可选。

### 定义正则表达式
---

定义正则表达式的方式有两种：
* 字面量方法
```
  var expression = /pattern[/flags];

  #例子
  var pattern1 = /at/g;
  var pattern2 = /[bc]at/i;
  var pattern3 = /.at/gi;
```
* 使用RegExp构造函数
```
  var expression = new RegExp(pattern [, flags]);
```
>**注1：**在第二种方法中，`pattern`和 `flags`都为字符串模式。
>**注2：**在ECMAScript3中，正则表达式字面量和RegExp构造函数创建的正则表达式不一样。前者始终共享同一个RegExp，后者每创建一个RegExp都是一个新实例。ECMAScript5中，使用正则表达式字面量必须像直接调用RegExp构造函数一样，每次都创建新的实例。

### 属性
---

RegExp的每个实例都具有下列属性:

| 属性      |  返回值  |                          描述                                  |
|------     |  :---:  |                          :---                                  |
| global    |  布尔值  |                    表示是否设置了g标志                          |
| ignoreCase|  布尔值  |                    表示是否设置了i标志                           |
| lastIndex |  整数    |             表示开始搜索下一个匹配的字符位置，从0算起              |
| multiline |  布尔值  |                    表示是否设置了m标志                           |
| source    |  字符串  |正则表达式字符串表示，按照字面量形式而非传入构造函数中的字符串模式返回 |

```
  var pattern1 = /\[bc\]at/i;

  alert(pattern1.global);           //false
  alert(pattern1.ignoreCase);       //true
  alert(pattern1.multiline);        //false
  alert(pattern1.lastIndex);        //0
  alert(pattern1.source);           //"\[bc\]at"

  var pattern2 = new RegExp("\\[bc\\]at", "i");
  
  alert(pattern2.global);           //false
  alert(pattern2.ignoreCase);       //true
  alert(pattern2.multiline);        //false
  alert(pattern2.lastIndex);        //0
  alert(pattern2.source);           //"\[bc\]at"
```


### RegExp实例方法
---

* `exec()`
* `test()`：模式与该参数匹配的情况下返回true。
* `toLocaleString()`
* `toString()`
* `valueOf()`

```
  # `exec()`
  var text = "cat, bat, sat, fat";
  var pattern1 = /.at/;

  var matches = pattern1.exec(text);
  alert(matches.index);              //0
  alert(matches[0]);                 //cat
  alert(pattern1.lastIndex);         //0

  # `test()`
  var text = "000-00-0000";
  var pattern = /\s{3}-\d{2}-\d{4}/;

  if(pattern.test(text)) {
    alert("The pattern was matched.");
  }

  # `toLocaleString()`和`toString()`方法都会返回正则表达式的字面量，与创建正则表达式的方式无关。
  var pattern = new RegExp("\\[bc\\]at", "gi");
  alert(pattern.toString());                 //  /\[bc\]at/gi
  alert(pattern.toLocaleString());           //  /\[bc\]at/gi

  # `valueOf()`
  #返回正则表达式本身
```

### RegExp构造函数属性
---

RegExp构造函包含一些属性(这些属性在其他语言中被看作是静态属性)。关于这些属性的另一个独特之处就是，可以通过两种方式访问他们。分别为：

* 长属性名
  * `input`：最近一次要匹配的字符串
  * `lastMatch`：最近一次的匹配项
  * `lastParen`：最近一次匹配的捕获组
  * `leftContext`：input字符串中lastMatch之前的文本
  * `multiline`：布尔值，表示是否所有表达式都是用多行模式
  * `rightContext`：Input字符串中lastMatch之后的文本

* 短属性名
  * `$_`
  * `$&`
  * `$+`
  * `$``
  * `$*`
  * `$'`

```
  var text = "this has been a short summer";
  var pattern = /(.)hort/g;

  if(pattern.test(text)) {
    alert(RegExp.input);             // this has been a short summer
    alert(RegExp.lastMatch);         // this has been a
    alert(RegExp.lastParen);         // summer
    alert(RegExp.leftContext);       // short
    alert(RegExp.multiline);         // s
    alert(RegExp.rightContext);      // false
  }
```
>**注：**值得注意的是，Opera不支持短属性名，且Opera不支持input、lastMatch、lastParen、multiline属性，IE不支持multiline属性。



有问题？发送 [issues](http://sythoney.me/about/) 给我~

