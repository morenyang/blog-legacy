---
title: JS学习笔记1:闭包
date: 2016-10-25
foreword: 简单地理解JavaScript中闭包的概念
---

我们学习JavaScript的时候，常常会听到别人说JS有一个重要的性质就是闭包。闭包可以说确实是一个JS的重要特性，而且并不好理解。<!-- more -->

#### 定义
先来看一下闭包直截了当的定义：

> 当函数可以记住并访问所在词法的作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。


## 作用域

要弄清楚这句话中的意思，我们首先要了解一下JavaScript变量的作用域。JavaScript中的变量无非分为两种：全局变量和局部变量。我们来看下面两个例子：

```JavaScript
// example 1
var a = 1;

function foo() {
  console.log(a);
}

foo();  // 1
```
在这段代码中我们使用`foo()`函数申请调用全局变量`a`，并输出，得到结果1。

```JavaScript
// example 2
function foo() {
  var a = 1;
}

console.log(a); // undefined
```
而在第二段代码中，我们在`foo()`函数中定义了局部变量`a`，但是无法在函数外部读取，因此没有办法输出`a`。

## 闭包的本质

那么问题来了：**我们要如何读取函数中的局部变量**？我们可以通过下面这个函数：
```JavaScript
// example 3
function foo() {
  var a = 1;

  function bar() {
    console.log(a); // 2
  }

  bar();
}

foo(); // 2
```
这段代码可以很方便的让我们通过`bar()`函数访问其外部作用域中的变量`a`。因为`bar()`被封闭在`foo()`内部，其具有一个涵盖`foo()`作用域的闭包。其实这段代码已经*很接近闭包的定义*，并且不便于我们观察和理解这个片段中的闭包是如何工作的。

我们再来看下面这段代码，它清晰地展示了闭包：
```JavaScript
// example 4
function foo() {
  var a = 1;

  function bar() {
    console.log(a);
  }

  return bar;
}

var baz = foo();

baz(); // 2
```
函数`bar()`的词法作用域能够访问`foo()`的内部作用域。然后我们将`bar()`函数本身当做一个类型值进行传递（在JavaScript中函数也可以作为一个变量）。

在`foo()`执行后，其返回值被赋值给变量`baz`并调用`baz()`，实际上只是通过不同的标识符引用调用了内部的函数`bar()`，而在这个例子中，`bar()`在自己定义的词法作用域以外执行。

由于`bar()`被声明在`foo()`中，**因此它涵盖了`foo()`内部作用域的闭包**，因此在`foo()`执行后，其内部的作用域并没有被回收。**而`foo()`内部的闭包可以供`bar()`在之后的任何时间内引用。**

**`bar()`依然持有对该作用于的引用，这个引用就叫做闭包。**

因此，我们可以简单理解：***闭包就是在函数内部定义一个函数，在函数内部和外部搭建起一座桥梁。***

-----

当然，无论我们用何种方式对函数类型的值进行传递，当函数在别处被调用时都可以观察到闭包，例如：
```JavaScript
function foo() {
  var a = 1;

  function baz() {
    console.log(a); // 1
  }

  bar(baz);
}

function bar(fn) {
  fn();
}

foo(); // 1
```
通过`bar()`调用`foo()`中的`baz()`，当调用这个内部函数时，它涵盖的`foo()`内部作用域的闭包就可以被观察到了。

当然传递函数也可以是间接的：
```JavaScript
var fn;

function foo() {
  var a = 1;

  function baz() {
    console.log(a);
  }

  fn = baz;
}

foo();
fn(); // 1
```

-----

当然，闭包还有一个重要的用途就是让函数中的变量始终保存在内存中。例如下面这个例子：
```JavaScript
// example 5
var add;

function foo() {
  var a = 1;
  add = function(){
    a++;
  }
  return function(){ // 为了方便我们把这个匿名函数叫做baz
    console.log(a);
  }
}

var bar = foo();

bar(); // 1

add()；

bar(); // 2 
```

在这段代码中，`bar()`实际上就是`foo()`返回的匿名函数（ *`baz()`* ）。由于bar是全局变量，因此`foo()`和他的内部函数就一直被存在了内存中。
当然我们也可以利用这个特性创造函数的私有变量和私有方法，例如我们如果尝试获取example 4中bar的a的值，会发现程序报错：
```bash
> console.log(bar.a)
undefined
undefined
```

## 几个需要注意的例子

### 例子1
我在查百度了解闭包这个概念的时候，看到了这样的一个函数：
```JavaScript
// example 6
var object = {
    name : "My Object",
    getNameFunc : function() {
        return function() { // 暂且称这个匿名函数为foo吧
            // console.log(this); // 这行代码帮助看到当前匿名函数的状态
            return name;
        };
    }
};

console.log(object.getNameFunc()()); 
// 两个括号是因为调用getNameFunc的时候返回了一个匿名函数（ `foo()` ），为了执行这个函数我们对其传参数（在这里是空参）
```
我们会理所当然的认为example 6这个函数会输出*My Object*，但实际上当我们运行这个函数的时候，控制台会输出 ` ReferenceError: name is not defined ` 。这是为什么呢，其实不难理解。

当我们调用了`object`中的`getNameFunc`函数时，它返回了一个匿名函数`foo`，在我们运行`foo`这个函数的时候，编译器会在它的作用域中查找局部变量`name`，但因为`foo`中并没有定义，因此编译器又回去他的上一级作用域`getNameFunc`中寻找`name`，然而在`getNameFunc`中我们也并没有定义，因此编译器返回到了更上一级也就是与`object`***平级***的作用域（也就是全局）中寻找`name`，然而还是没有找到，因此只能输出`undefined`。

### 例子2
如果我们在for循环中试着调用闭包函数：
```JavaScript
// example 7
for(var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 500);
}
```
我们又会理所当然的认为，控制台会分别输出数字 1~5，每隔0.5秒一次，每次一个。
但实际上，这段代码运行时会每秒一次地输出5次6。

解释一下原因：
  - 首先说一下6是怎么来的：函数的循环终止条件是i<=6，因此条件首次成立时i的值是6。因此输出的结果是循环结束时i的最终值。我们可以在这个`for`执行完后向控制台输入`console.log(i)`来看到。
  - `for`循环中所有`setTimeout`的回调函数，都在循环结束后才被执行，因此每次都会输出一个6出来
  - 回调函数中的五个函数虽然被分别定义，其实都在共享和调用同一个作用域中的同一个`i`。

我们想要处理这个问题，显然需要更多的作用域。有的人可能会想到把`setTimeout`当做一个表达式来处理，像这样：
```JavaScript
for(var i = 1; i <= 5; i++) {
  (function() {
    setTimeout(function() {
      console.log(i);
    }, i * 500);
  })();
}
```
然而，当你这面这段代码输入到控制台中的时候，会发现其实并没有什么卵用。虽然拥有更多的词法作用域但其实作用域还是空的。因此我们在解决这个问题的时候，还需要增加一些局部变量，例如：
```JavaScript
for(var i = 1; i <= 5; i++){
  (function(){
    var j = i;
    setTimeout(function() {
      console.log(j);
    }, j * 500);
  })();
}
```
我们在函数每次执行的时候都预先保存了一个`j`为当前`i`的副本，并在接下来的`setTimeout`中调用它。
稍微改写简化一下：
```JavaScript
for(var i = 1; i <= 5; i++){
  (function(i){
    setTimeout(function() {
      console.log(i);
    }, i * 500);
  })(i);
}
```
那么问题就解决了。

##### 另一种解决方法：块作用域
我们可以在每次迭代的时候都新建一个作用域，换句话说就是我们每次迭代都需要一个块作用域。而`let`声明可以用来劫持块作用域，其本质上就是将这个块作用域换成一个可以被关闭的作用域。因此我们可以这么写：
```JavaScript
for(var i = 1; i <= 5; i++) {
  let j = i;
  setTimeout(function () {
    console.log(j);
  }, j * 500);
}
```
但其实，在`for`循环头部使用let声明还会有一个特殊的行为：变量在每次迭代的时候都会声明，随后每个迭代都会使用上一个迭代结束时的值来初始化这个变量。因此我们可以这么写：
```JavaScript
for(let i = 1; i <= 5; i++){
  setTimeout(function() {
    console.log(i);
  }, i * 500);
}
```
这段代码和我们之前解决问题时所输出的内容是一样的。

## 最后扯两句
闭包这个概念看起来好像很高大上，但其实我们平时在编写JavaScript的时候会经常遇到，只可能我们并没有发现而已。例如ajax中的请求、定时器、事件监听等，只要使用了*回调函数*，实际上都在使用闭包。

-----
*这个系列的文章用来当做我学习JavaScript时的备忘录和重点记录，以便需要的时候会看。因为我最近在读《你不知道的JavaScript上卷》和《JavaScript设计模式》这两本书，所以文章里的内容大部分都会摘自这里。*