---
title: JS学习笔记:提升
date: 2018-04-25 13:59:43
foreword: 对不起，会编译原理真的可以为所欲为
---

## 引子

昨天金山笔试的第一题

```js
console.log(a) // A
var a = 1;
var setA = function() {
  a = 2;
}
function setA() {
  a = 3;
}
console.log(a) // B
setA();
console.log(a) // C
```
A、B、C 三处分别输出什么

我第一行就写了 `报错 a is not defined`

于是很完美的错了。

大多数人会想当然的觉得JavaScript是按照代码顺序从上往下一行一行执行的，然而事实并不是这样。

## 执行之前

实际上，JavaScript在执行之前，会先经过编译的过程。与大多数需要需要编译再执行的语言一样，JavaScript再编译的时候回经过词法分析、语法分析、代码生成几个阶段。

在词法分析阶段，代码的字符串会根据解析规则，被分解成一个一个词法单元，例如`var a = 1;` 这行代码，就会被分解成`var` `a` `=` `1` `;`几个单元。

在语法分析阶段，程序会将语法分析阶段得到的词法单元们转换成一个由元素逐级嵌套的所组成的代表程序语法结构的树（抽象语法树）。

在代码生成阶段，抽象语法树会被转换成可执行代码。例如`var a = 1;`这一行代码会被转换成一组机器指令，用来创建一个叫做a的变量，然后将一个值储存在a中。

## 声明和赋值的顺序

问题就出在这儿，JavaScript编译器在编译的时候，会先把包括变量和函数的所有声明放到程序的所有其他部分前面，先行处理。这个过程叫做提升（Hoisting）。也就是说，在一段JavaScript代码中，所有变量都会先声明，后赋值，例如：

```js
a = 1;
a = a + 1;
var a;
console.log(a); // 2
```
这一段代码执行时并不会报错，因为`var a;`这一句生命语句会被最先执行，再接着执行后续的步骤，它的实际执行顺序大致是这样的：

```js
var a;
a = 1;
a = a + 1;
console.log(a);
```

注意，被提前的是**声明**，而**赋值**语句或其他运行逻辑会保留在原来的位置，例如：

```js
a = 1;
a = a + 1;
var a = 0;
console.log(a) // 0
```
这段代码最终会输出0。

<br/>

同样的，函数的声明也会被提升。这就有点像其他语言中，函数声明写在`main`函数后面依然能被正常执行一样。此外，**每个作用域**都会进行提升操作。例如：

```js
foo();

function foo() {
  console.log(a); // undefined
  var a = 1;
}
```
这段代码实际上会按照类似这个顺序执行：

```js
function foo(){
  var a;
  console.log(a);
  a = 1;
}
```

<br/>

### 函数表达式不会被提升
另外要注意的是，函数声明会被提升，但是**函数表达式**并不会被提升。

```js
// part 1
foo(); // Uncaught TypeError: foo is not a function

var foo = function baz(){
  console.log(1);
}

// part 2
bar(); // Uncaught TypeError: bar is not a function

console.log(bar)
var a = true;
var bar = a ? console.log(1) : console.log(2);
```

在这两段代码中，报的错并不是`ReferenceError`，而是`TypeError`，因为标识符`foo`和`bar`实际已经被分配了，但是此时他们的值是`undefined`，调用时便会产生TypeError错误。

同时，即使是具名的函数表达式，名称标识符在赋值之前也无法在所在作用域中使用：

```js
foo(); // TypeError
bar(); // ReferenceError

var foo = function bar() {
	console.log(1);
}
```
这段代码会以此顺序被执行：
```js
var foo;

foo();
bar();

foo = function() {
  var bar = ...self...
  console.log(1);
}
```

<br/>

## 函数优先提升
如果一段代码中既有变量声明，又有函数函数声明，此时函数声明的提升优先级高于变量。

比如最上面笔试题的变体：

```js
console.log(a) // undefined
var a = 1;

function setA() {
  a = 2;
}

var setA = function() {
  a = 3;
}
console.log(a) // 1
setA();
console.log(a) // 3
```

这段代码会以这样的顺序执行：

```js
function setA() {
  a = 2;
}
var a;
console.log(a)
a = 1;
var setA = function() {
  a = 3;
}
console.log(a);
setA();
console.log(a);
```
在`var setA = function(){ //... }`这一行，由于前面已经把`setA`这个标识符分配出去了，因此`var`声明会被忽略掉。

你可能会想，如果同时声明两个一样名称的函数会怎么样呢，比如：

```js
console.log(a) // undefined
var a = 1;

function setA() {
  a = 2;
}

function setA() {
  a = 3;
}

console.log(a) // 1
setA();
console.log(a) // 3
```

答案是后声明的函数会覆盖先声明的函数。

在《你不知道的JS》中，关于提升，有这么一段代码：

```js
foo() // 2

if(true) {
  function foo() {
    console.log(1)
  }
} else {
  function foo() {
    console.log(2)
  }
}
```
书中指出函数声明不会按以上代码暗示的那样被条件判断所控制，输出结果为`2`。但是目前在浏览器和Node中执行的结果均会报错。我们编写代码时应尽量规避这种写法，以防不必要的错误。

## 结语和其他

JavaScript中的提升到这里已经基本上介绍明白了，总结起来就是**把所有变量和函数的声明挪到了上下文的最前面，函数的提升优先级比变量高**，标识符的赋值不受影响。

如果你觉得变量和函数的提升给你带来了不少麻烦，你可以这么规避它：

- 使用严格模式 (声明`"use strict"`)
- 在ES6中使用`let`或`const`声明变量常量或函数

### 参考

- 《你不知道的JS上卷》第一部分第1章、第4章
- [变量提升 - MDN](https://developer.mozilla.org/zh-CN/docs/Glossary/Hoisting)