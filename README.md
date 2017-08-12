# ES6-
本文作者：IMWeb coolriver 原文出处：IMWeb社区 未经同意，禁止转载

## 简介
ES6的设计目标，是使得JavaScript语言可以用来编写大型复杂的应用程序，成为企业级开发语言。ES6将于2015年6月正式发布。

现在最新版本的Nodejs上可以通过`--harmony`参数开启ES6的部分支持。如果旧的Nodejs版本不支持或者想体验更多的ES6 特性
可以 使用Google的[traceur](https://github.com/google/traceur-compiler)工具。traceur可以在前端编译ES6代码，也可以
在node上 编译或执行ES6代码。

- traceur在node上安装： npm install -g traceur
- 直接执行ES6文件： traceur test.es6.js
- 编译ES6文件到ES5文件：traceur --script test.es6.js --out test.es5.js ES6在很方面都对ES5有增强，下面将从不同方面对ES6在ES5上的加强进行介绍。

## 新增块级作用域（let与const关键字）
### let声明变量

在ES5中，除了全局作用域外，限定所声明的变量的作用域是函数作用域。这使得ES5在很多情况下为了模拟块级作用域（避免变量名污染）需要使用立即执行的匿名函数。在ES6中新增了声明块级使用域变量的关键字let。与var相比，使用let声明的变量有如下特点：
- let声明的变量所在的作用域为块级。
- let声明的变量不存在变量提升。
- let声明的变量不允许重复声明，否则会报错。 使用let可以替代ES5中为了模拟块级作用域而使用的立即执行的匿名函数
```
//ES5实现方法:
(function(){
    for (var i = 0;i < 10;i++){
        //doSomething
    }
})();

//ES6实现方法:
for (let i = 0;i < 10;i++){
    //doSomething
}
```
- 需要注意的是，ES6中函数本身的作用域在其块级作用域之类（相当于使用let声明了），这样，在if条件内声明的函数就不会像ES5因函数提升而总会被声明。

### const声明常量

  ES6中可以使用const关键字来声明常量，被声明的常量不能被修改。与使用let声明的变量类似，const声明的常量为块级作用域，不存在变量提升，且不可重复声明。
　　
  const只限定就是所以的地址不能改变，意味着如果声明的目标为对象，那么对象的属性是可以修改的。书中建议如果要使对象为常量的话可以配合Object.freeze()函数来实现:
```
const foo = Object.freeze({a:1});

//foo = {b:2} 无法修改foo
//foo.a = 2   无法修改foo.a
```
以上方法中的Object.freeze()函数本身有局限性，它只能冻结对象的属性不被修改，并不能冻结它的属性的属性被修改。如果要实现将对象内部所有属性冻结，需要使用自定义的强化的冻结函数。关于深度冻结对象的方法在codewars上的一个题目有描述，具体方案如下：

```
Object.deepFreeze = function (object) {
    Object.freeze(object);
    Object.keys(object).forEach(function(key) { 
        if(typeof(object[key]) == 'object') 
        return Object.deepFreeze(object[key]); 
    });
}
```
通过以上deepFreeze即可实现完全将对象常量化。效果如下：

```
const foo = Object.freeze({a:[1]}); //使用原始的冻结函数
foo.a.push(2); //本操作可以使foo.a变为[1,2]

const foo2 = Object.deepFreeze({a:[1]}); //使用深度冻结函数
foo2.a.push(2); //本操作无法改变foo2.a
```
### 全局对象属性
　　全局对象是最顶层的对象，在浏览器环境指的是window对象，在Node.js指的是global对象。ES5规定，所有全局变量都是全局对象的属性。
　　ES6规定，var命令和function命令声明的全局变量，属于全局对象的属性；let命令、const命令、class命令声明的全局变量，不属于全局对象的属性。
### 变量的解构赋值
　　ES6中新增了一种赋值方法，可以批量地从对象（或数组）中提取出相应的值赋值到一组对应的变量上。下面为数组形式的解构赋值:
```
//数组的解构赋值
var [a,b,c] = [1,2,3];

//相当于
var a = 1,b = 2,c = 3;
```
　　下面为对象形式的解构赋值:
```
//对象的解构赋值方式一
var {bar, foo} = {foo: "aaa", bar: "bbb"};
foo //"aaa"
bar //"bbb"

//对象的解构赋值方式二
var {first: f, last: l} = {first: "hello", last: "world"};
f //"hello"
l //"world"
```
解构赋值可以带来很多便利：

- 变量交换 ：[x, y] = [y, x]
- 函数返回多个值赋值给多个变量
- 可以方便地将一组参数与变量名对应起来。
- 方便提取JSON数据
- 方便设置函数参数默认值。（解构赋值可以指定默认值）
- 遍历Map：for (let [key, value] of map){}
- 加载模块时指定需要加载的方法：const {SourceMapConstumer, SourceNode} = require("source-map")
### 字符串扩展

- ES6中提供了一级新的方法来解决ES5里某些中文字符（UTF-16编码大于0XFFFF）编码的问题。另外ES5中使用码点"\uXXXX"来表示字符的方法不能表示大于0XFFFF的字符，在ES6中可以使用大括号将码点括起来就可以表示大于0XFFFF的字符："\u{XXXX}"。
- ES6对正则表达式添加了u修饰符，用来正确处理大于\uFFFF的Unicode字符。点（.）字符在正则表达式中，解释为除了换行以外的任意单个字符。对于码点大于0xFFFF的Unicode字符，点字符不能识别，必须加上u修饰符：

```
var s = "𠮷";

/^.$/.test(s) // false
/^.$/u.test(s) // true
```
*传统上，JavaScript只有indexOf方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6又提供了三种新方法。

- [ ] includes()：返回布尔值，表示是否找到了参数字符串.
- [ ] startsWith()：返回布尔值，表示参数字符串是否在源字符串的头部.
- [ ] endsWith()：返回布尔值，表示参数字符串是否在源字符串的尾部.
- repeat()返回一个新字符串，表示将原字符串重复n次。可以方便地构建由重复字符或字符串构成的字符串：
```
"x".repeat(3) // "xxx"
"hello".repeat(2) // "hellohello"
```
字符串模板。模板字符串用``进行定义，中间使用到的变量值使用${}进行嵌入。使用字符串模板可以很方便地将变量与字符串结合，而不用使用大量的加号进行拼接：
```
$("#result").append(`
  There are ${basket.count} items
   in your basket,${basket.onSale}
  are on sale!
`);
```
### 数值扩展

- ES6提供了新的表示二进制和八进制数据的写法，二进制用前缀0b表示，八进制用前缀0o表示。新的八进制表示方法弥补了ES5中前缀0写法存在的问题。

```
0b111110111 === 503 // true
0o767 === 503 // true
```

- ES6将ES5中的全局方法：isFinite(),isNaN(),parseInt(), parseFloat()移至Number上，分别变为Number.isFinite(),Number.isNaN(),Number.parseInt(), Number.parseFloat()。这样做是为了逐步减少全局方法，使语言逐步模块化。
- Math对象新增的方法:
