---
title: JavaScript基础知识
tags: js
---

### JavaScript
---

1. 出现的主要目的：处理服务器端语言负责的一些输入验证操作。
2. 如今的作用：处理复杂的计算和交互
3. 特点：拥有闭包，匿名函数，元编程等特性。
4. 完整的JavaScript实现：
    * 核心(ECMAScript)：提供核心功能
    * DOM：提供访问和操作网页的方法和接口。
    * BOM：提供与浏览器交互的方法和接口。

<br>

### ECMAScript(5)
---

1. 规定这门语言的组成部分：
    * 语法
    * 类型
    * 语句
    * 关键字
    * 保留字
    * 操作符
    * 对象

<br>

### DOM(Document Object Model)
---

1. 解决的问题：标准化Netscapet和微软的Web开发。
2. DOM1：
    * DOM核心:规定如何 **映射基于XML的文档结构**
    * DOM HTML：添加HTML的对象和方法。         

>注：规定映射文档结构的目的是，简化任意部分的访问和操作。

3. DOM2：添加和用户界面(View)、事件(Event)、范围和遍历(Traversal and Range)等细分模块，通过对象接口增加了对css(Style)的支持。
4. DOM3：引入统一方式加载和保存(DOM Load and Save)文档的方法，新增验证方法(DOM Validation)。

<br>

### 新学习到的知识
---

#### `<script>`元素

1. 放置位置：按照惯例，所有该元素都一起放在`<head>`元素中。但，此种做法会导致页面延缓显示，于是，将该元素全部放在`</body>`前。
2. 属性：
    1. `async`(可选)：表示应该立即下载脚本，但不应该妨碍页面中的其他操作。
    2. `charset`(可选)：表示通过src属性指定的代码的字符集。(少用)
    3. `defer`(可选)：表示脚本可以延迟到文档全部被解析和显示之后再执行。
    4. `src`(可选)：引入外部文件
    5. `type`(可选)：表示编写代码使用的脚本语言的内容类型。    

>注：`defer`的作用和`1`的作用相同。使用`<script>`元素的方式有两种方式：直接再页面中嵌入JavaScript代码和包含外部JavaScript文件。

#### 基础知识

1. 嵌入代码和外部文件一般考虑的问题：
    * 可维护性
    * 可缓存
    * 可适应未来
2. 未初始化变量为`undefined`类型，未声明变量类型为`undefined`类型。
```
    var message;   //undefined
    function test(){
        var message = "hi";
    }
    
    test();
    alert(message);  //undefined
```
3. 数据类型：
    * 基本数据类型：`Undefined`,`Null`,`Boolean`,`Number`,`String`
    * 复杂数据类型：`Object`
4. 不支持任何创建自定义类型的机制。
5. 检测基本数据变量的类型操作符：`typeof`,检测引用变量类型操作符：`instanceof`。
6. 未初始化的变量执行`typeof`操作符会返回`undefined`值，而对未声明的变量执行`typeof`操作符同样也会返回`undefined`值。
```
    var message ;
    alert(typeof message);     //undefined
    alert(typeof age);        //undefined
    
```
7. `True`和`False`不是`Boolean`值，只是标识符。
8. 当对象调用`isNaN()`方法时会先调用`valueOf()`方法，确定返回的值是否可以转换为数值。如果不能，则基于这个返回值再调用`toString()`方法测试返回值。
9. `label`语句：使用`label`语句可以在代码添加标签，以便将来使用，语法：
```
    // 语法：label : statement
    
    //示例
    start: for (var i = 0;i < count; ++i){
        alert(i);
    }
```
这个例子中定义的`start`标签可以在将来由`break`或`continue`语句引用。加标签的语句通常与`for`语句等循环语句配合使用。
10. `with`语句，目的主要是为了简化多次编写同一个对象的工作。语法：
```
    //语法：with(expression) statement

    //示例
    var qs = location.search.substring(1);
    var hostname = location.hostname;
    var url = location.href;
    
    //使用 with 语句如下：
    with(location){
        var qs = search.substring(1);
        var hostname = hostname;
        var url = href;
    }
```
>注：由于大量使用`with`语句会导致性能下降，同时也会给调试代码造成困难，因此在大型应用程序中不建议使用`with`语句。

11. `ECMAScript` 中的`switch`语句：`expression`和`value`可以使用任何数据类型。
12. `ECMAScript`中的`function`参数在内部是用一个数组来表示的,所有的参数传递的都是值，不可能通过引用传递参数。
13. 没有重载。
14. 基本类型和引用类型的值：
    * 基本类型：简单的数据段
    * 引用类型：可能由多个值构成的对象

>注：在很多语言中，字符串以对象的形式来表示，因此被认为是引用类型。WCMAScript放弃了这一传统。

14. 数据类型值的种类：
    * 基本类型值：保存在 **栈** 内存中
    * 引用类型值：保存在 **堆** 中    

>注：这两种值不仅在内存中存储的位置不同，执行赋值操作之后内存变化也不相同。

15. 规定：所有的引用类型都是`Object`示例。
16. 执行环境(作用域),定义了变量或函数有权访问的其他数据决定了他们各自的行为。每个执行环境都有一个与之关联的**变量对象**，环境中定义的所有变量和函数都保存在这个对象中。
17. 在web中，全局被认为时window对象。
18. 没有块作用域。
```
    if(true){
        var color = "blue";
    }
    alert(color);      // "blue"
    
```
19. 垃圾回收机制：
    * 标记清除法(mark-and-sweep)：
    * 引用计数法(reference counting)：因该方法会导致 **循环引用** ，被废弃。
    
20. 管理内存：分配给web浏览器的可用内存数量通常要比分配给桌面应用程序的少。     
    目的：防止运行JavaScript的网页耗尽全部系统内存而导致系统崩溃。

<br>

有问题？发送 [issues](http://sythoney.me/about/) 给我~
