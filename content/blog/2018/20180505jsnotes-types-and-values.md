---
title: JS学习笔记:变量、类型和值
date: 2018-05-05 
foreword: undefined is not a function. 
---

不论在哪一种语言中，变量、类型和值都会是我们最常用到的几个概念。刚开始学JavaScript的时候，我会觉得这几样东西似乎没那么复杂——特别是对于这种弱类型语言——感觉我们做事的时候直接往变量里面赋个值就好了。然而当我从一个小白成长成一个菜鸟了以后，就发现这件事真的没有这么简单。

## 你是谁
类型。

在JavaScript中，**只有值才有类型**。JavaScript一共有七种内置类型

- null
- undefined
- boolean
- number
- string
- symbol *ES6新增*
- object

我们可以把他们分成两类，object为一类——我们常把他叫做**引用类型**，剩下六个为一类——我们称他们为**基本类型**。

我们可以用`typeof`关键字来判断某个值属于哪个类型，他会返回一个字符串：

- `typeof 1; // "number"`
- `typeof "1" // "string"`
- `typeof true // "boolean"`
- `typeof undefined // "undefined"`
- `typeof Symbol() // "symbol`
- `typeof {} // "object"`
- `typeof [] // "object"`
- `typeof setTimeout // "function" 特别注意！`
- `typeof null // "object" 特别注意！`

需要特别注意的是，使用typeof处理null的时候会返回**"object"**，这是JavaScript种的一个feature。如果我们想判断一个值的类型是不是null，你可以这么做：
```js
var foo = null;
(!a && typeof a === "object") // true
```

另外，typeof在处理函数的时候，会返回**"function"**。这是因为它虽然属于object的一个子类型，但是由于它是一个可执行的对象，所以进行了一些特别处理。

## 你叫什么名字
变量。

JavaScript中的变量不需要主动定义类型，你也可以认为变量始终没有类型，它只持有某个值。换句话说——变量只是我们的一个户头，不同户头里可能会有不同类型和不同值的货币，我们可以随时把某个户头里的货币的值或者类型换掉。

在JavaScript运行环境下，**基本类型的变量的值会被储存在stark中**，他们占据的空间小，大小固定。**引用数据类型的变量的值会被存储在heap中，并在stark里存放一份这个对象的指针**；引用数据类型常大小不固定，保存形式也不固定，如果存在stark中可能会影响程序的性能。

要给一个变量名分配一个值，需要进行查询。在JavaScript中有两种查询类型——LHS（Left Hand Side）查询和RHS(Right Hand Side)查询。这里的Left和Right指的是赋值的时候，变量位于”=“的左侧还是右侧。

LHS查询指的是查找到某个变量的容器，并为这个容器赋值。例如下面这一句话就是LHS查询：

```js
var a = 1;
```

而RHS查询指的是找到某个变量容器中的值。例如：
```js
console.log(a);
```
这句话中，由于`a`没有被赋予任何值，因此我们需要查找并取得a的值，才能将这个值传递给`console.log`。

在JavaScript中，声明一个变量并对它赋值，一定是新建一个**值**并将这个值压入栈中，无论是原始类型赋值还是引用类型赋值。例如`var b = a`，一定会新建一条和`a`的值一样的值，并将这个值压入栈中。这个概念对于原始类型值，大家一定都没有争议。如果到了引用类型值上，还是一样的吗——

答案是，对的。别忘了我们在前面所讲的，引用类型变量的数据会被保存在堆中，但同时会在栈中存放一份*对堆区存放的数据的引用*——你可以把它当做一个传送门。假设我们执行了
```js
var a = {};
var b = a; // 语句1
```
实际上在语句1中，只是新建了一个和`a`一样的传送门，并把这个传送门存放到栈内。

另外，在JavaScript中，所有的参数传递都是复制传递的——传送的一定是**值**，只不过**这个值是指向堆中某个变量的地址**。例如：

```js
var a = "num", b = {};

function foo(key, obj) {
	obj[key] = 1;
	a = 2;
}

// 这里传送的a和b分别是栈中存放a（数字）的地址和存放b（传送门）的地址
foo(a, b);
console.log(a); // 2
console.log(b); // {"num" : 1}
```

关于这个问题，在知乎上有不少讨论。传送门：[JavaScript中函数都是值传递吗？](https://www.zhihu.com/question/51018162) [javascript 变量赋值与函数参数传递的方式是什么？](https://www.zhihu.com/question/22473205/answer/21481258)

## 你在吗

JavaScript中，表示一个变量的值是空的时候，会有两种方式——null 和 undefined。

虽然他们都表示空，但实际上还是有差别的——

- 变量没有被赋一个确定的值的时候，她持有的值是`undefined`
- `null`表示“该值为空”
- `undefined`表示“缺少一个值”，也就是说这里本该有值，但是还没有被定义。 

我们在将`null`和`undefined`转成数字的时候也可以看出他们的差别——

```js
Number(null); // 0
Number(undefined); // NaN
```

这里还要多说一句，其实还有一个概念叫 “undeclared”（未声明）。变量在声明但没有赋值的时候为`undefined`；在作用域内没有声明过的变量，是undeclared的。例如：

```js
var a;
a; // undefined
b; // ReferenceError: b is not defined 报错
```
这个例子可以很好的看出undefined和undeclared的区别，但！是！假如你用了 typeof 来处理：

```js
var a;
typeof a; // "undefined"
typeof b; // "undefined" 没有报错！
```

对于undefined和undeclared变量，typeof 都会返回"undefined"，并且没有报错，在这一点上我们要特别注意。

## 最后

变量、值和值的类型，在JavaScript中常常是一个大坑。实际运用时，一定一定一定要想办法规避掉这些坑的地方。同时，在面试的时候，他们也是常常被问到并且能把你坑到的一个点。一定要小心、谨慎、弄清楚他们的关系和原理。

### 参考
- 《你不知道的JavaScript上卷》第一部分第1章
- 《你不知道的JavaScript中卷》第一部分第1章、第2章
- [undefined与null的区别 - 阮一峰](http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null)

