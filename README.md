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
