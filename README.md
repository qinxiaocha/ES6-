# ES6-
本文作者：IMWeb coolriver 原文出处：IMWeb社区 未经同意，禁止转载

##简介
ES6的设计目标，是使得JavaScript语言可以用来编写大型复杂的应用程序，成为企业级开发语言。ES6将于2015年6月正式发布。

现在最新版本的Nodejs上可以通过`--harmony`参数开启ES6的部分支持。如果旧的Nodejs版本不支持或者想体验更多的ES6 特性
可以 使用Google的[traceur](https://github.com/google/traceur-compiler)工具。traceur可以在前端编译ES6代码，也可以
在node上 编译或执行ES6代码。

- traceur在node上安装： npm install -g traceur
- 直接执行ES6文件： traceur test.es6.js
- 编译ES6文件到ES5文件：traceur --script test.es6.js --out test.es5.js ES6在很方面都对ES5有增强，下面将从不同方面对ES6在ES5上的加强进行介绍。

##新增块级作用域（let与const关键字）
###let声明变量
在ES5中，除了全局作用域外，限定所声明的变量的作用域是函数作用域。这使得ES5在很多情况下为了模拟块级作用域（避免变量名污染）需要使用立即执行的匿名函数。在ES6中新增了声明块级使用域变量的关键字let。与var相比，使用let声明的变量有如下特点：
- let声明的变量所在的作用域为块级。
- let声明的变量不存在变量提升。
- let声明的变量不允许重复声明，否则会报错。 使用let可以替代ES5中为了模拟块级作用域而使用的立即执行的匿名函数
