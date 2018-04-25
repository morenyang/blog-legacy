---
title: JS学习笔记2:Style Guide 备忘录
date: 2017-04-17
tags:
---

        

内容摘抄自 [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) 和 [Airbnb JavaScript Style Guide 中文版](https://github.com/sivan/javascript-style-guide/blob/master/es5/README.md)

内容不定时更新，这里就当做**备忘**和**划重点**

## Airbnb JavaScript Style Guide() {

### 引用 References

*   Use `const` for all of your references; avoid using `var`. eslint: [`prefer-const`](http://eslint.org/docs/rules/prefer-const.html), [`no-const-assign`](http://eslint.org/docs/rules/no-const-assign.html)

    > Why? This ensures that you can’t reassign your references, which can lead to bugs and difficult to comprehend code.

```js
// bad
var a = 1;
var b = 2;
// good
const a = 1;
const b = 2;
```

*   If you must reassign references, use `let` instead of `var`. eslint: [`no-var`](http://eslint.org/docs/rules/no-var.html) jscs: [`disallowVar`](http://jscs.info/rule/disallowVar)

    > Why? `let` is block-scoped rather than function-scoped like `var`.
    

```js
// bad
var count = 1;
if (true) {
  count += 1;
}
// good, use the let.
let count = 1;
if (true) {
  count += 1;
}
```

*   Note that both `let` and `const` are block-scoped.

```js
// const and let only exist in the blocks they are defined in.
{
  let a = 1;
  const b = 1;
}
console.log(a); // ReferenceError
console.log(b); // ReferenceError
```

### 数组 Arrays

*   Use the literal syntax for array creation. eslint: [`no-array-constructor`](http://eslint.org/docs/rules/no-array-constructor.html)

```js
// bad
const items = new Array();
// good
const items = [];
```

*   Use [Array.push](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/push) instead of direct assignment to add items to an array.

```const someStack = [];
// bad
someStack[someStack.length] = 'abracadabra';
// good
someStack.push('abracadabra');
```

*   Use array spreads `...` to copy arrays.
_使用拓展运算符`...`来拷贝数组_

```js
// bad
const len = items.length;
const itemsCopy = [];
let i;
for (i = 0; i < len; i += 1) {
  itemsCopy[i] = items[i];
}
// good
const itemsCopy = [...items];
```

*   To convert an array-like object to an array, use [Array.from](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from).
_使用`Array.from`将类数组对象（如`String`）转为数组_

```js
const foo = document.querySelectorAll('.foo');
const nodes = Array.from(foo);
// 例如
console.log(Array.from("abc")); // ["a", "b", "c"]
```

*   Use return statements in array method callbacks. It’s ok to omit the return if the function body consists of a single statement [following](#8.2). eslint: [`array-callback-return`](http://eslint.org/docs/rules/array-callback-return)
_在数组方法回调中使用return语句。如果函数体由单个语句组成，则可以省略返回。_

```js
// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
// good
[1, 2, 3].map(x => x + 1);
// bad
const flat = {};
[[0, 1], [2, 3], [4, 5]].reduce((memo, item, index) => {
  const flatten = memo.concat(item);
  flat[index] = flatten;
});
// good
const flat = {};
[[0, 1], [2, 3], [4, 5]].reduce((memo, item, index) => {
  const flatten = memo.concat(item);
  flat[index] = flatten;
  return flatten;
});
// bad
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  } else {
    return false;
  }
});
// good
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  }
  return false;
});
```

### 解构 Destructuring

*   Use object destructuring when accessing and using multiple properties of an object. jscs: [`requireObjectDestructuring`](http://jscs.info/rule/requireObjectDestructuring)
_使用解构存取和使用多属性对象以减少临时引用属性。_

    > Why? Destructuring saves you from creating temporary references for those properties.
    
```js
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;
  return `${firstName} ${lastName}`;
}
// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}
// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

*   Use array destructuring. jscs: [`requireArrayDestructuring`](http://jscs.info/rule/requireArrayDestructuring)

```js
const arr = [1, 2, 3, 4];
// bad
const first = arr[0];
const second = arr[1];
// good
const [first, second] = arr;
```

*   Use object destructuring for multiple return values, not array destructuring. jscs: [`disallowArrayDestructuringReturn`](http://jscs.info/rule/disallowArrayDestructuringReturn)
_需要回传多个值时，使用对象解构，而不是数组解构，因为增加属性或者改变排序不会改变调用时的位置_

> Why? You can add new properties over time or change 
the order of things without breaking call sites.


```js
// bad
function processInput(input) {
  // then a miracle occurs
  return [left, right, top, bottom];
}
// the caller needs to think about the order of return data
const [left, __, top] = processInput(input);
// good
function processInput(input) {
  // then a miracle occurs
  return { left, right, top, bottom };
}
// the caller selects only the data they need
const { left, top } = processInput(input);
```
### 函数 Functions

*   Use named function expressions instead of function declarations. eslint: [`func-style`](http://eslint.org/docs/rules/func-style) jscs: [`disallowFunctionDeclarations`](http://jscs.info/rule/disallowFunctionDeclarations)

    > Why? Function declarations are hoisted, which means that it’s easy - too easy - to reference the function before it is defined in the file. This harms readability and maintainability. If you find that a function’s definition is large or complex enough that it is interfering with understanding the rest of the file, then perhaps it’s time to extract it to its own module! Don’t forget to name the expression - anonymous functions can make it harder to locate the problem in an Error’s call stack. ([Discussion](https://github.com/airbnb/javascript/issues/794))

```js
// bad
function foo() {
  // ...
}
// bad
const foo = function () {
  // ...
};
// good
const foo = function bar() {
  // ...
};
```

*   Never name a parameter `arguments`. This will take precedence over the `arguments` object that is given to every function scope.

```js
// bad
function foo(name, options, arguments) {
  // ...
}
// good
function foo(name, options, args) {
  // ...
}
```

*   Use default parameter syntax rather than mutating function arguments.

```js
// really bad
function handleThings(opts) {
  // No! We shouldn't mutate function arguments.
  // Double bad: if opts is falsy it'll be set to an object which may
  // be what you want but it can introduce subtle bugs.
  opts = opts || {};
  // ...
}
// still bad
function handleThings(opts) {
  if (opts === void 0) {
    opts = {};
  }
  // ...
}
// good
function handleThings(opts = {}) {
  // ...
}
// 例如
function handleThings(opts = {}) {
    console.log(opts);
}
handleThings() // {}
handleThings({name: 'Moren'}) // {name: 'Moren'}
```

### [](#箭头函数-Arrow-Functions "箭头函数 Arrow Functions")箭头函数 Arrow Functions

<a name="arrows--use-them"></a><a name="8.1"></a>

*   When you must use function expressions (as when passing an anonymous function), use arrow function notation. eslint: [`prefer-arrow-callback`](http://eslint.org/docs/rules/prefer-arrow-callback.html), [`arrow-spacing`](http://eslint.org/docs/rules/arrow-spacing.html) jscs: [`requireArrowFunctions`](http://jscs.info/rule/requireArrowFunctions)

    > Why? It creates a version of the function that executes in the context of `this`, which is usually what you want, and is a more concise syntax.
    > 
    >   Why not? If you have a fairly complicated function, you might move that logic out into its own function declaration.

```js
// bad
[1, 2, 3].map(function (x) {
  const y = x + 1;
  return x * y;
});
// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```

*   If the function body consists of a single expression, omit the braces and use the implicit return. Otherwise, keep the braces and use a `return` statement. eslint: [`arrow-parens`](http://eslint.org/docs/rules/arrow-parens.html), [`arrow-body-style`](http://eslint.org/docs/rules/arrow-body-style.html) jscs:  [`disallowParenthesesAroundArrowParam`](http://jscs.info/rule/disallowParenthesesAroundArrowParam), [`requireShorthandArrowFunctions`](http://jscs.info/rule/requireShorthandArrowFunctions)
_如果一个函数适合用一行写出并且只有一个参数，那就把花括号、圆括号和 return 都省略掉。如果不是，那就不要省略。_

    > Why? Syntactic sugar. It reads well when multiple functions are chained together.

```js
// bad
[1, 2, 3].map(number => {
  const nextNumber = number + 1;
  `A string containing the ${nextNumber}.`;
});
// good
[1, 2, 3].map(number => `A string containing the ${number}.`);
// good
[1, 2, 3].map((number) => {
  const nextNumber = number + 1;
  return `A string containing the ${nextNumber}.`;
});
// good
[1, 2, 3].map((number, index) => ({
  [index]: number,
}));
```

### 比较运算符 &amp; 等号 Comparison Operators &amp; Equality

*   Use `===` and `!==` over `==` and `!=`. eslint: [`eqeqeq`](http://eslint.org/docs/rules/eqeqeq.html)

    <a name="comparison--if"></a><a name="15.2"></a>

*   Conditional statements such as the `if` statement evaluate their expression using coercion with the `ToBoolean` abstract method and always follow these simple rules:

    *   **Objects** evaluate to **true**
    *   **Undefined** evaluates to **false**
    *   **Null** evaluates to **false**
    *   **Booleans** evaluate to **the value of the boolean**
    *   **Numbers** evaluate to **false** if **+0, -0, or NaN**, otherwise **true**
    *   **Strings** evaluate to **false** if an empty string `''`, otherwise **true**

```js
if ([0] && []) {
  // true
  // an array (even an empty one) is an object, objects will evaluate to true
  // 数组为空，但它作为一个对象存在
}
```

*   Use shortcuts for booleans, but explicit comparisons for strings and numbers.

```js
// bad
if (isValid === true) {
  // ...
}
// good
if (isValid) {
  // ...
}
// bad
if (name) {
  // ...
}
// good
if (name !== '') {
  // ...
}
// bad
if (collection.length) {
  // ...
}
// good
if (collection.length > 0) {
  // ...
}
```

*   For more information see [Truth Equality and JavaScript](https://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108) by Angus Croll.


*   Use braces to create blocks in `case` and `default` clauses that contain lexical declarations (e.g. `let`, `const`, `function`, and `class`).

    > Why? Lexical declarations are visible in the entire `switch` block but only get initialized when assigned, which only happens when its `case` is reached. This causes problems when multiple `case` clauses attempt to define the same thing.

eslint rules: [`no-case-declarations`](http://eslint.org/docs/rules/no-case-declarations.html).

```js
// bad
switch (foo) {
  case 1:
    let x = 1;
    break;
  case 2:
    const y = 2;
    break;
  case 3:
    function f() {
      // ...
    }
    break;
  default:
    class C {}
}
// good
switch (foo) {
  case 1: {
    let x = 1;
    break;
  }
  case 2: {
    const y = 2;
    break;
  }
  case 3: {
    function f() {
      // ...
    }
    break;
  }
  case 4:
    bar();
    break;
  default: {
    class C {}
  }
}
```

*   Ternaries should not be nested and generally be single line expressions.

    eslint rules: [`no-nested-ternary`](http://eslint.org/docs/rules/no-nested-ternary.html).

```js
// bad
const foo = maybe1 > maybe2
  ? "bar"
  : value1 > value2 ? "baz" : null;
// better
const maybeNull = value1 > value2 ? 'baz' : null;
const foo = maybe1 > maybe2
  ? 'bar'
  : maybeNull;
// best
const maybeNull = value1 > value2 ? 'baz' : null;
const foo = maybe1 > maybe2 ? 'bar' : maybeNull;
```

*   Avoid unneeded ternary statements.

    eslint rules: [`no-unneeded-ternary`](http://eslint.org/docs/rules/no-unneeded-ternary.html).

```js
// bad
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;
// good
const foo = a || b;
const bar = !!c;
const baz = !c;
```

### 模块 Modules


*   Do not export directly from an import.

    > Why? Although the one-liner is concise, having one clear way to import and one clear way to export makes things consistent.

```js
// bad
// filename es6.js
export { es6 as default } from './AirbnbStyleGuide';
// good
// filename es6.js
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

*   Only import from a path in one place.
eslint: [`no-duplicate-imports`](http://eslint.org/docs/rules/no-duplicate-imports)

    > Why? Having multiple lines that import from the same path can make code harder to maintain.

```js
// bad
import foo from 'foo';
// … some other imports … //
import { named1, named2 } from 'foo';
// good
import foo, { named1, named2 } from 'foo';
// good
import foo, {
  named1,
  named2,
} from 'foo';
```