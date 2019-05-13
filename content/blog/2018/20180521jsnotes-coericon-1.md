---
title: JS学习笔记:类型转换之「抽象值操作」
date: 2018-05-21
tags:
---

这怕是JavaScript中最坑、最有毒的一个部分了。

将值从一种类型转换成另一种类型叫做类型转换。例如：

```js
var a = 1;
var b = String(a);  // "1" 显式转换
var c = "" + a;     // "1" 隐式转换
```

在JavaScript中，我们把上面列举的两类转换都称为 _强制类型转换_ ，按照显式和隐式的区别我们可以将强制类型转换分为 _显式强制类型转换_ 和 _隐式强制类型转换_ 。

当然要注意，这里的显式和隐式都是相对的。假如你对JavaScript运行中的所有将要执行的类型转换都牢记于心，那当然可以把他们全部称为显式强制类型转换。本文主要参考《你不知道的JavaScript中卷》，因此参照书中的标准来界定显式和隐式。

JavaScript中的强制类型转换总是返回**标量基本类型值**，例如字符串、数字和布尔值，不会返回对象和函数。

这篇文章会学习抽象值操作。

## 抽象值操作

### ToString

基本类型的字符串化规则为：

- `null`转换为`"null"`
- `undefined`转换为`"undefined"`
- `true`转换为`true`
- 数字的字符串化则遵循通用规则，对于极小和极大的数使用指数形式

对于对象来说，字符串化的时候会调用对象的`toString() `方法，并使用其返回值。如果没有自行定义的话，`toString() `方法会默认返回内部属性[[Class]]的值。例如以下的例子：

```js
"" + {}; // "[object Object]"

"" + [1,2,3]; // "1,2,3"
"" + {toString: function(){return "1"}}; // "1"
```

`toString()` 可以被显式调用，或在需要字符串化的时候自动调用。

### JSON 字符串化

`JSON`应该是我们在JS中最常用的序列化和反序列化工具之一了。`JSON.stringify()`在将对象序列化为字符串的时候也用到了ToString。但是需要注意，JSON字符串化并非严格意义上的强制类型转换。

对于大多数基本类型值来说，JSON字符串化的结果和`toString() `是差不多的，只不过序列化的结果总是字符串：

```js
JSON.stringify(1);  // "1"
JSON.stringify("1");  // ""1"" (带有双引号的字符串)
JSON.stringify(null); // "null"
JSON.stringify(true); // "true"
```

有些值`JSON`是无法处理的，例如：`undefined`、`function`、`symbol` 和包含循环引用（对象之间相互引用）的对象。我们把这些它们称作 _不安全的JSON值_ 。

相对的，所有 _安全的JSON值（JSON-safe）_ 都可以使用`JSON.stringify()`字符串化。

`JSON.stringify()` 在对象中遇到 `undefined`、 `function` 和 `symbol`的时候会自动忽略他们，在数组中这回返回null（为的是保证数据的下标不变），在遇到循环引用的对象时会报错。例如：

```js
JSON.stringify(undefined); // undefined
JSON.stringify(function(){}); // undefined

JSON.stringfiy(
  [1, undefined, function(){}, 4]
); // "[1,null,null,4]"
JSON.stringify(
  {a: 1, b: function(){}, c: undefined }
);  // "{"a":1}"
JSON.stringify(
  {toString: function(){return "1"}}
); // "{}"

var a = {};
var b = {a: a};
a.b = b;
JSON.stringify(a); // Uncaught TypeError: Converting circular structure to JSON
```

如果对象中定义了`toJSON() `方法，`JSON`字符串化的时候会首先调用该方法，然后用它的返回值来进行序列化。如果你对某些非法JSON值定义了`toJSON()`方法，并返回一个安全的JSON值，那么这个值就能被字符串化了。 _注意，对象是不自带`toJSON() `方法的，需要你主动定义它。_ 例如：

```js
var a = {};
var b = {a: a};
a.b = b;

b.toJSON = function(){
  return {};
};

JSON.stringify(a); // "{"b":{}}"

var foo = function(){}
foo.toJSON = function(){return 123}
JSON.stringify(foo); // "123"

var bar = {
  a: undefined,
  toJSON: function (){ return {a: null} }
}
JSON.stringify(bar); // "{"a":null}"
```

`toJSON()`返回的并不一定是JSON字符串化后的值（这样其实会对字符串再做一次字符串化），而应当是一个**适当的值**，**可以是任何类型**，然后再由`JSON.stringify()`对其字符串化。

## ToNumber

ToNumber相比ToString来说就简单很多。

将基本数据类型转换为数字的规则为：
- `true`转换为`1`
- `false`转换为`0`
- `undefined`转换为`NaN`
- `null`转换为`0`
- 对字符串的转换遵循通用规则，处理失败时返回`NaN`，对以0开头的十六进制数按十进制处理而非十六进制。

对于对象来说，它们会先被转换成相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其转换成数字。

对象转换成基本类型值的规则为：首先检查是否有`valueOf()`方法，如果有并且返回基本类型值，就使用改值进行转换。如果没有则使用`toString()`方法的返回值（如果存在）来进行强制类型转换。如果`valueOf()`和`toString()`都不反悔基本类型值，则会产生TypeError错误。

几个例子：

```js
var a = {valueOf: function(){return "1"}};
var b = {toString: function(){return "2"}}
Number(a); // 1
Number(b); // 2

var c = [1,2,3];
Number(c); // NaN
c.valueOf = function(){
  return 1;
}
Number(c); // 1

var d = [];
Number(d); // 0
d.toString = function(){
  return 1;
}
Number(d); // 1

var e = [1,2,3];
e.toString = function(){
  return this.join("") // 123
}
Number(e) //123
```

### ToBoolean

boolean的两个字`true`和`false`分别代表着 _真_ 和 _假_ 。在JavaScript中，数字`1`和`0`与`true`和`false`并不等价。

对于基本类型值来说，JavaScript中的值可以分为两类：
- 可以被强制类型转换成`false`的值
- 其他（可以被强制类型转换成`true`）的值

以下的值为 _假值_ ：
- `undefined`
- `null`
- `false`
- `+0`、`-0`和`NaN`
- `""`

除了假值表以外的所有值都可以理解为 _真值_ 。

一般来说，所有的对象都是真值，但是有一些特殊情况，我们可以把他们叫做 _假值对象_ 。

先来说几个例子——

```js
var a = new Boolean(false);
var b = new Number(0);
var c = new String("");
var d = new Boolean(0);

var foo = Boolean(a && b && c &&d);
foo; // true
```

`foo`为`true`，说明`a`、`b`、`c`、`d`都为true。

这些封装了假值的对象都并非假值。

我们来看看真正的假值对象——他们是来自浏览器在某些特定条件下创造的对象，例如`document.all`：

```js
document.all; // 返回一个 HTMLAllCollection 对象
!!document.all; // false
```

这是一个类数组对象，由DOM提供（而非JavaScript引擎）提供给JavaScript程序使用。它以前曾是一个真正意义上的对象，布尔强制类型转换的结果为true，但现在它是一个假值对象——并且这已经死一个被废止的方法了。

由于很多JavaScript程序依赖`document.all`来判断是否是旧版浏览器，因此一直没有把它去掉。

再说一说几个真值的情况——虽然刚才说过了假值表以外的都是真值，但是你还可能会遇到一些比较刁钻的状况：

```js
var a = Boolean("false");
a; // true

var b = Boolean("0");
b; // true

var c = Boolean("\"\"");
c; // true
```

对于字符串来说，除了`""`以外都是真值。同时，对于`[]`、`{}`、`function(){}`这一类的对象都是真值。

## 最后
这一部分的内容是真的坑、真的经常会被问到。对于类型转换，一定要多小心、多注意。

### 参考
- 《你不知道的JavaScript中卷》第一部分第四章