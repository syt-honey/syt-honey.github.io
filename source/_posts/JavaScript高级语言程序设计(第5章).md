---
title: Object和Array类型
tags: js
---


#### `Object`类型
---

1. 创建`Object`示例的方式：
    * new
    * 使用对象字面量 

>注：对象字面量定义对象时，不会调用`object`构造函数。

2. 访问属性方式：
    * 点表示
    * 方括号    

>注：如不强制要求，则使用点表示法访问属性。

<br>

#### `Array`类型
---

1. 创建的基本方式：
    * new
    * 使用对象字面量   

>注：对象字面量定义对象时，也不会调用构造函数。

2. 数组的`length`不是只读的，如：
```
    var colors = ["red", "blue", "green"];   //创建一个包含3个字符串的数组
    colors.length = 4;
    alert(colors[3]);         //undefined
```
3. 检测数组
```
    if(value instanceof Array){
        //对数组执行某些操作
    }
```
存在的问题：它假定只有一个全局执行环境。如果网页中有多个框架，那实际上就有多个不同的全局执行环境，从而存在多个不同版本的构造函数。

`ECMAScript5`解决方法：
```
    if(Array.isArray(value)){
        //对数组执行某些操作
    }
```
对于尚未实现该方法的浏览器中准确的检测数组：
```
    function isArray(value){
        return Object.prototype.toString.call(value) == "[object Array]";
    }
```
由于原生数组的构造函数与全局作用域无关，因此使用`toString()`就能保证返回一致的值。
同样，也可以基于这一思想来测试某个值是不是原生函数或正则表达式：
```
    //函数
    function isFunction(value){
        return Object.prototype.toString.call(value) == "[object Function]";
    }
    
    //正则表达式
    function isRegExp(value){
        return Object.prototype.toString.call(value) == "[object RegExp]";
    }
```

4. 转换方法    
数组继承的`toLocaleString()`,`toString()`和`valueOf()`方法，在默认情况下都会以逗号分隔的字符串形式返回数组项。而如果使用`join()`方法，则可以使用不同的分隔符来创建这个字符串。
```
   var colors = ["red","green","blue"];
   alert(colors.join(",")); 
   alert(colors.join("||"));                //red||green||blue
   
```

5. 栈方法
`pop()`和`push()`是数组默认的删除元素和添加元素方法。
6. 队列方法
    * `shift()`和 `push()`，将数组最前端元素移除并返回，数组长度减1，和往数组的末尾添加一个元素，数组长度加1.
    *  `unshift()`和`pop()`，在数组前端添加一个元素并返回数组的长度，和将数组末尾移除一项。

7. 重排序方法
数组中已经存在的两个可以直接用来重排序的方法：`reverse()`和`sort()`。
由于`reverse()`不够灵活才有的`sort()`。`sort()`方法默认的升序排列，排列时会调用数组项的`toString()`转型方法然后比较得到的字符串。   
```
    var value = [0, 1, 5, 10, 15];
    value.sort();
    alert(value);   //0,1,10,15,5
```
即便是数组为数值类型，该方法仍然会调用`toString()`方法来对数组项进行比较。可见，这不是我们想要的结果。`sort()`方法可以接受一个比较函数作为参数。一个简单的比较函数：
```
    function compare(value1, value2) {
        if(value1 < value2) {
            return -1;
        } else if(value1 > value2) {
            return 1;
        } else{
            return 0;
        }
    }
```
将此函数传给`sort()`方法，如下：
```
    var values = [0, 1, 5, 10, 15];
    values.sort(compare);
    alert(values);      //0,1,5,10,15
```
对于数值类型或者其`valueOf()`方法会返回数值类型的对象类型，可以使用一个更简单的比较函数：
```
    function compare(value1, value2){
        return value2 - value1;
    }
```
具体实现原理请参见：[js中Array.sort()实现原理](https://blog.csdn.net/coder_chenz/article/details/77156047)

8. 操作方法
    * `concat()`,基于当前数组中的所有项创建一个新的数组，然后将接收到的参数添加到这个副本的末尾。
    * `slice()`，基于当前数组中的一个或多个项创建一个新数组，可接受一个或两个参数，即返回的起始和终止位置。
    * `splice()`，向数组中部插入项,会改变原始数组
        * 删除，需指定两个参数
        * 插入
        * 替换
```
    //concat()
    var colors = ["red","green","blue"];
    var colors2 = colors.concat("yellow",["black","brown"]);
    
    alert(colors);       //red,green,blue
    alert(colors2);      //red,green,blue,yellow,brown
    
    //slice()
    var colors3 = colors.slice(0);
    var colors4 = colors.slice(1, 3)
    
    alert(colors3);      //red,green,blue
    alert(colors4);      //green,blue
    
    //splice()
    //删除第一项
    var removed = colors.splice(0, 1);
    alert(colors);      //green,blue
    alert(removed);     //red
    
    //从位置1开始插入两项
    removed = colors.splice(1, 0, "yellow", "orange");
    alert(colors);      //green,yellow,orange,blue
    alert(removed);     //返回一个空数组
    
    //插入两项，删除1项
    removed = colors.splice(1, 1, "red", "purple");
    alert(colors);      //green,red,pruple,orange,blue
    alert(removed);     //yellow
```

9. 位置方法：都接受两个参数，第一个是要查找的项和表示要查找的起点位置(可选)。
    * `indexOf()`：从头部开始往后查找(0开始)。
    * `lastIndexOf()`：从尾部开始往前查找。
```
    var numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
    
    alert(numbers.indexOf(4));     //3
```
10. 迭代方法：
    * `every()`
    * `some()`：和上面一个方法否是返回`true`或`false`
    * `filter()`
    * `map()`：和上面一个方法都是返回一个数组
    * `forEach()`：无返回值
```
    //filter()
    var numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
    var filterResult = numbers.filter(function(item, index, array){
        return (item > 2);
    });
    
    alert(filterResult);             //[ 3, 4, 5, 4, 3]
    
    //map()
    var numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
    var mapResult = numbers.map(function(item, index, array){
        return item * 2;
    });
    
    alert(mapResult);             //[ 2, 4, 6, 8, 10, 8, 6, 4, 2]
    
```

11. 归并方法：    
都接受两个参数：一个在每一项上调用的函数和作为归并基础的初始值。
    * `reduce()`
    * `reduceRight()`
```
    var values = [1, 2, 3, 4, 5];
    var sum = values.reduce(function(prev, cur, index, array){
        return prev + cur;
    });
    
    alert(sum);   //15
```

<br>

有问题？发送 [issues](http://sythoney.me/about/) 给我~
