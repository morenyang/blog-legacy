---
title: 在React中动态加载模块
date: 2018-09-28
foreword: 好久不见
---

## 起因

在React 项目中常会遇到打包出来的bundle 过大而引发的页面加载速度过慢或是性能问题。因此，如果能够将使用的模块按需在使用时动态加载，就可以很大程度地减少资源浪费并优化首次加载速度。

例如，在使用highlight.js 时，官方为我们提供了176种语言的支持，每种语言支持包的大小从1K到几十K不等，一次性全部加载就需要load 1MB的资源。而如果采用按需加载的方式，每次就只需要load 几K的资源。想想就很酷。

在webpack v2 之后的版本中，可以使用 `import()` 来完成代码分割和动态载入。借助这个feature ，可以实现上文预想的功能。

## 关于 import()

JavaScript 中的模块是纯静态的。使用`import` 和`export` 的语句必须放在模块的顶层，它们会在 __编译__ 时执行（而不是运行时）。因此，如果你在JS代码编译之后修改了module 中的内容，即使还没有用到它，也无法对运行中的程序造成影响。另外，如果想把`import` 语句放在`if` 代码块中或函数也是不行的，这么做会报语法错误。

这个feature 带来的好处是可以提高编译器的执行效率，但是无法在运行时加载模块。

为了解决这个问题，有一个[提案](https://github.com/tc39/proposal-dynamic-import)建议引入`import() ` 方法来进行动态加载。例如：

```js
const specifier = './module.js';
import(specifier)
  .then(someModule => someModule.foo());
```

`import()` 方法和`import` 语句能使用的参数是一样的，它使用起来类似于一个函数。在ES6中， `import()` 方法会返回一个`Promise` 对象。当模块加载完成时，实现这个`Promise` 。

通过`import()` 方法，可以按需求加载模块，例如：

```js
button.addEventListener('click', () => {
    import('./dialogBox.js')
        .then(dialogBox => {
            dialogBox.open();
        })
        .catch(error => {
            /* Error handling */
        })
});
```

或者将需要加载的模块放在判断中：

```js
if(condition) {
    import("module")
}
```

还可以配合Promise的特性来加载模块：

```js
Promise.all([
    import('./module1.js'),
    import('./module2.js'),
    import('./module3.js'),
]);
```

在webpack 中，如果使用`import()` 函数来加载模块，webpack 在打包时会自动将模块拆分，并仅在需要使用时才载入这个模块。

## 在React 中动态加载模块

在React 中使用这个feature，需要合理地搭配生命周期函数。

以上文所说的应用场景为例，它的基础用法大致如下：

```js
import React from 'react';
import Lowlight from 'react-lowlight';
import PropTypes from 'prop-types';
import js from 'highlight.js/lib/languages/javascript';

// 使用之前要先对模块进行注册
Lowlight.registerLanguage('js', js);

class CodeRenderer extends React.Component {
  static propTypes = {
    value: PropTypes.string,
    language: PropTypes.string,
  };

  static defaultProps = {
    value: '',
    language: 'js',
  };

  render() {
    return <Lowlight value={this.props.value} language={this.props.language} />;
  }
}

export default CodeRenderer;
```


## 参考

- https://webpack.js.org/guides/code-splitting/
- http://2ality.com/2017/01/import-operator.html
- https://serverless-stack.com/chapters/code-splitting-in-create-react-app.html