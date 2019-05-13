---
title: JS学习笔记:闭包！闭包！
date: 2018-04-27
tags:
foreword: Reference Not Error
---

之前我写过一篇关于介绍闭包的文章，这次去金山笔试也有两道题是和闭包有关的 ~~（我还也写错了一道，不过错误的地方和闭包没关系）~~，所以今天再来聊一聊和闭包有关的东西。

其实上一篇文章写的并不好，长篇大论了一番也没说清楚闭包的本质是什么。这次我尽量说得浅显易懂一点。

## 闭包从哪里来

闭包有两个很重要的关键词，一个是函数，一个是作用域。

我们都知道，一般来说，JavaScript中语句能访问的作用域有它本身所在的作用域和它父级以上的作用域，例如：

```js
var a = 1;

function foo() {
  var b = 2;
  console.log(a + b); // 语句A
}

foo(); // 3
console.log(b) // ReferenceError: b is not defined 语句B 注意这里是引用错误而不是undefined 因为什么语句只会被提升到它所在的作用域中 
```

上面的代码中，A处语句能访问的作用域为他所在的函数`foo()`的作用域和它的父级作用域（全局作用域），而B处语句只能访问它所在的作用域（全局作用域），而不能获取函数`foo()`作用域中的变量。

OK，以上的内容大家应该都是认同的了。但是我们知道，对于JavaScript这个磨人的小妖精，函数是一个对象。我们可以在某一个函数中，把另一个函数作为返回值返回出来（例如工厂模式），就想下面这段代码：

```js
function printNameFactory(name) {
  return function() {
    console.log(`My Name is ${name}`);
  }
}

var printMyName = printNameFactory('Moren');
var printCatName = printNameFactory('XiaoMaoMi');

printMyName(); // My Name is Moren
printCatName(); // My Name is XiaoMaoMi
```

到此为止我们的思路还是很和谐的，上面的代码应该很浅显易懂，我向`printNameFactory`方法传入我的名字，然后他返回一个方法给我，这个方法可以快捷的打印出我的名字。我们也无妨可以把这个方法改一改，让他变得更长，看起来更高级一点 ~~，让客户觉得我们更牛逼一点~~：

```js
function printNameFactory(name) {
  var _name = name || 'ChunChunMao';
  function print() {
    console.log(`My Name is ${_name}`);
  }
  return print;
}

var printName = printNameFactory();
printName(); // ChunChunMao

```

凭着感觉第一眼看好像这段函数没有什么问题，但是如果你仔细想一想，好像和我们之前说的内容有点不一样：

- 现在`printName`的值应该是`function (){console.log('My Name is ${_name}')} // 这里由于编辑器限制没有办法打反引号 用引号代替`
- 现在`printName`所在的作用域是全局作用域
- 现在`_name`所在的作用域是`printNameFactory`函数的作用域

由以上三条似乎可以推导出，执行`printName`函数时应该报引用错误。这和我们的实验结果似乎大相径庭。但是结果就摆在那儿了，我们只能去猜测是什么原因导致的——最合理的解释是——难道这是个bug！

![](./1.jpg)

合理的解释应该是：**`printName`可以访问原来函数（`printNameFactory`）中的作用域。**

那么问题又来了！按照正常的思路，`printNameFactory`这段函数执行完后，它的整个内部作用域应该被销毁，因为现在的引擎正常都会有相应的垃圾回收机制。也就是说，我们实验产生的结果表示：**产生`printName`所指向函数的函数（即`printNameFactory `）执行时所产生的函数作用域并没有被销毁。**

这个问题的正确答案是——闭包导致了刚才我们观察到的结果。**当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前的词法作用域之外执行。**

## 闭包的本质？

刚才我们已经看清楚并分析出了闭包产生的现象，但是肯定有人会问，闭包的本质到底是什么。

还记得我们这一段文字最开始提到的闭包的两个关键词——函数和作用域吗——**实际上，闭包是由函数以及创建该函数的词法环境（可以理解成作用域）组合而成。这个环境包含了闭包创建时所能访问的所有局部变量（也就是作用域中的所有变量）**。

这个概念可能有些抽象，回想刚才的那段代码，我把它简化一下——

```js
function foo() {
  let a = 1;
  
  function bar() {
    console.log(a);
  }
  
  return bar;
}

var baz = foo(); // 语句A
baz(); // 2 语句B
```

语句A执行后，其返回值`bar()`被赋值给了`baz`。因此当我们调用`baz()`的时候，可以理解为我们在调用`foo()`里面的`bar()`；

拜闭包所赐，`foo()`执行后，其内部的词法环境（作用域）~~得以续命~~并没有被销毁，这个作用域可以供`bar()`在之后任何时间内调用。换句话说，**`bar()`一直持有对该词法环境的引用，这个引用就叫做闭包。**

值得一提的是，无论使用何种方式对函数类型的值进行传递，当函数在别处调用时都可以观察到闭包。例如：

```js
function foo() {
  var a = 2;
  function baz() {
    console.log(a); // 2
  }
  
  bar(baz);
}

function bar(fn) {
  fn(); // fn -> baz, baz 拥有foo()的作用域
}

foo();
```

当然，无论使用何种方式将内部函数传递到词法作用域之外，他都会持有对原始定义作用域的引用，无论在何处执行这个函数都会有闭包。例如：

```js
var fn;

function foo() {
  var a = 2;
  function baz() {
    console.log(a);
  }
  fn = baz; // 将baz分配给全局变量
}

function bar() {
  fn(); // fn -> baz， 即使是在全局作用于依然有用对foo()作用域的引用
}

foo();
bar(); // 2
```

## 我好像在哪里用过

在我们平时编写的代码里，闭包的应用其实无处不在。例如一段延时执行的代码：

```js
function printLater(message) {
  var _msg = message || '';
  setTimeout(function print() {
    console.log(_msg);
  }, 5000);
}

printLater('Miao'); // 5秒后打印 Miao
```

这段代码将一个内部函数（`print`）传递给`setTimeout`。在代码运行后五秒，才会执行`print`函数，这时`print`函数中就带有`printLater`作用域的闭包，因此还保留有变量`_msg`的引用。

除了`setTimeout`函数，在JavaScript中，基本上所有的非即时操作如定时器、事件监听、异步请求等，只要使用了回调函数，实际上都使用到了闭包。

还有一道经典的前端题目，就是从1到10每隔一秒输出一个数字。大多数人的第一直觉是

```js
for(var i = 1; i <= 10; i++) {
  setTimeout(function() {
    console.log(i);
  }, i * 1000);
}
```

然而实际情况是，这段程序会分10次每隔一秒输出一次11。这是因为在第一个`setTimeout`中的匿名函数（我们之后把这个函数称为`timer`）函数执行之前，for循环已经全部执行完了，此时i的值为11。而之后`timer`函数执行的时候，所带的作用域其实是共享的同一个作用域，因此实际上只有一个i。

如果我们想要按照题目的设想输出，则需要给每一个`setTimeout`或者`time`构建一个封闭的私有的作用域和变量，因此我们需要这么写：

```js
for(var i = 1; i <= 10; i++) {
  (function() {
    var j = i;
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000)
  })();
}

// 改进一下
for(var i = 1; i <= 10; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000)
  })(i);
}
```

在上面的代码中，我们构建了一个立即执行的代码块（IIFE），在for循环迭代时，IIFE会为每一个迭代都生成一个新的作用域，使得函数的回调可以将新的作用域封闭在每个迭代内部。

## 闭包与模块

利用闭包的特性，可以创造模块。

我们拿刚才那个打印名字的代码为例。你通过`printNameFactory`创造出了一个可以随时随地打印自己名字的方法，但是你自恋的产品经理想要每天换一个名字，而傲娇的他又不想调用`printNameFactory`方法。这时候我们就可以用模块的方式来满足他的需求：

```js
function printNameFactory(name){
  var _name = name;
  function changeName(name) {
    _name = name;
  }
  function print() {
    console.log(`${_name}`);
  }
  return {
    changeName: changeName,
    print: print
  };
}

var namePrinter = printNameFactory('Moren');
namePrinter.print(); // Moren
namePrinter.changeName('YANG');
namePrinter.print(); // YANG
namePrinter._name; // undefined 语句A
```

在这个方式中，我们通过return，将模块中的一些方法暴露出来供外部使用。

来分析一下这些代码。

首先我们必须执行`printNameFactory`函数来创建一个模块实例。

其次，这段函数执行后会返回一个Object供我们使用，这个Object中含有对内部函数的引用。同时我们保证函数的内部数据变量是隐藏且私有的状态，例如A处语句并不能访问到`namePrinter`中的`_name`变量。我们可以将这个对象类型的返回值看做本质上是模块的公共API。如果你不理解他们，你也可以把它们类比做Java中的`private`和`public`对变量和方法的影响。

这个对象类型的返回值最终被赋值给外部变量`namePrinter`，以此我们可以通过他来访问API中的属性方法，例如`namePrint.print()`。

当然我们并不是一定要从模块中返回一个实际的对象，你也可以直接返回一个内部函数，例如最初的那个`namePrintFactory`，或者更好的例子如jQuery。在jQuery中，`jQuery`和`$`就是jQ模块的一个公共API，但他们本身都是函数。

你也可以通过IIFE的方式创建一个被立即调用的函数并生成相应模块：

```js
var morenPrinter = (function myPrinter(name){
  var _name = name || '';
  function changeName(name) {
    _name = name;
  }
  function print() {
    console.log(`${_name}`);
  }
  return {
    changeName: changeName,
    print: print
  };
})('Moren');

morenPrinter.print(); // Moren
```
这个例子中`myPrinter`会被立即调用，并将返回的对象传递给`MorenPrinter`。

模块是我们在JavaScript中组织和使用代码最常用的模式，关于模块的概念其实还有很多可以学习的，下次单独开一篇文章来讨论。

## 小结

闭包对于JavaScript真的真的是一个非常重要的概念。只有了解闭包才能更好地运用JavaScript。总结起来，就是：

- 函数会产生闭包；
- 函数不论在什么位置，都持有对**产生它的词法环境**的引用，也就是说能访问这个词法环境中的变量。

### 参考
- 《你不知道的JavaScript上卷》第一部分第5章
- [闭包 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)