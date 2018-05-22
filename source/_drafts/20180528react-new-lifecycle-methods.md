---
title: React新生命周期函数和迁移路径
tags:
---

> _本文内容主要参考自 [static-lifecycle-methods](https://github.com/reactjs/rfcs/blob/master/text/0006-static-lifecycle-methods.md) ，并加上了自己的一些理解。_

在今年三月底的时候React 发布了 16.3 版本。这次更新主要有两个内容——新的生命周期函数和context API（[React v16.3.0: New lifecycles and context API](https://reactjs.org/blog/2018/03/29/react-v-16-3.html)）。

对于生命周期函数，主要有以下改变（[Update on Async Rendering](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html)）：
- 以下生命周期函数将在以后的版本（17.0）中加上`UNSAFE_`前缀： 
	- `componentWillMount `
	- `componentWillReceiveProps `
	- `componentWillUpdate `
- 添加了两个生命周期函数：
	- static `getDerivedStateFromProps `
	- `getSnapshotBeforeUpdate `

旧的React 生命周期示意图如下
![](/blog/images/180528/2.png)

新的React 生命周期示意图如下（[react-lifecycle-methods-diagram](https://github.com/wojtekmaj/react-lifecycle-methods-diagram/)）:
![](/blog/images/180528/1.jpg)


本文会简单说明一些旧的生命周期函数可能造成的问题以及如何迁移到新的生命周期函数。

## 为什么要修改生命周期函数
这次添加新的生命周期函数和弃用旧的生命周期函数的主要原因是旧版组件API中的函数存在一些异步渲染的潜在安全缺陷。举几个常见的问题（[Common problems](https://github.com/reactjs/rfcs/blob/master/text/0006-static-lifecycle-methods.md#common-problems)）：

- 在`componentWillMount` 中初始化Flux store 时，如果这个store或其依赖在未来发生变化，就可能会导致一些问题。应该尽量规避这些不确定的情况。
- 在`componentWillMount` 中添加事件监听或事件订阅，并且你可能准备在`componentWillUnmount` 中删除他们。如果在组件初次render之前发生中断或出错，则会导致泄露。
- 在`componentWillMount` 、`componentWillUpdate` 、`componentWillReceiveProps` 或`render`中调用了非幂等（Non-idempotent ）外部函数，例如注册了可能被多次调用的回调函数等。

## 例子和解决方案

### 在 _Mount_ 期间执行异步操作获取数据

很多人这么做是想尽可能早的进行数据加载，特别是在性能底下的移动设备上——因为在初次`render` 和`componentDidMount` 之间可能会间隔几百毫秒的时间。这种情况看似可以在Mount完成之前获取到数据。

然而实际情况并不是这样的——不论你在`componentWillMount` 还是`componentDidMount` 期间进行了异步数据加载，这些获得来的数据都不会在初次render之前完成加载，在实践中，React总是会在`componentWillMount` 之后立即执行`render` ——因此无论哪种情况都需要进行第二次渲染。更何况`componentWillMount` 这个函数本身就不是让你用来执行异步操作的。

在新版本中，由于去除了`componentWillMount` 函数，因此应当将异步数据获取的函数移入`componentDidMount` ：

#### Before

```js
class ExampleComponent extends React.Component {
  state = {
    externalData: null,
  };

  componentWillMount() {
    asyncLoadData(this.props.someId).then(externalData =>
      this.setState({ externalData })
    );
  }

  render() {
    if (this.state.externalData === null) {
      // Render loading UI...
    } else {
      // Render real view...
    }
  }
}
```

#### After
```js
class ExampleComponent extends React.Component {
  state = {
    externalData: null,
  };

  componentDidMount() {
    asyncLoadData(this.props.someId).then(externalData => {
      // 注意如果组件在请求加载完成之前被卸载，将会触发
      // "cannot update an unmounted component". 警告
      // 你可以通过跟踪实例变量的装载状态来避免这种情况
      this.setState({ externalData });
    });
  }

  render() {
    if (this.state.externalData === null) {
      // 尝试尽早地创造缓存
      // asyncLoadData(this.props.someId);

      // Render loading UI...
    } else {
      // Render real view...
    }
  }
}
```

如果你真的很迫切的需要提升你的加载速度，你可以尝试在`render` 中调用一次异步请求，以便尽早地创造外部缓存，在`componentDidMount` 执行的时候，如果刚才请求创造的缓存命中，则可以大幅加快异步操作获取数据的速度。

### 设定组件初始状态

有一部分人会在`componentWillMount` 中执行`setState` 来初始化状态。对于这种情况，可以将这一部分操作放在构造函数或属性初始值设置的时候就可以了。

#### Before
```js
class ExampleComponent extends React.Component {
  state = {};

  componentWillMount() {
    this.setState({
      currentColor: this.props.defaultColor,
      palette: 'rgb',
    });
  }
}
```

#### After
```js
class ExampleComponent extends React.Component {
  state = {
    currentColor: this.props.defaultColor,
    palette: 'rgb',
  };
}
```

### 从 _Props_ 或 _State_ 中派生新的 _State_

这种情况指的是由 _props_ 计算出一些render 时需要用到的值。在16.3之前帮版本下，常常会使用`componentWillReceiveProps` 函数来依据props 更新state。在新的版本下，通常将`getDerivedStateFromProps` 函数用在此情况下。 _（这个生命周期函数在组件创建（create）时以及每次接收到新props 时都会被调用。）_ 

#### Before
```js
class ExampleComponent extends React.Component {
  state = {
    derivedData: computeDerivedState(this.props)
  };

  componentWillReceiveProps(nextProps) {
    if (this.props.someValue !== nextProps.someValue) {
      this.setState({
        derivedData: computeDerivedState(nextProps)
      });
    }
  }
}
```

#### After
```js
class ExampleComponent extends React.Component {
  // 在 constructor 中设置初始状态，
  // 或者直接设置初始属性值。
  state = {};

  static getDerivedStateFromProps(nextProps, prevState) {
    if (prevState.someMirroredValue !== nextProps.someValue) {
      return {
        derivedData: computeDerivedState(nextProps),
        someMirroredValue: nextProps.someValue
      };
    }

    // 返回null 表示state 没有状态改变
    return null;
  }
}
```

仔细阅读代码你会发现，上面这个例子中，在组件的 _state_ 里保存了一部分当前 _props_ 的状态。这么做的目的是可以使`getDerivedStateFromProps` 函数像先前的`componentWillReceiveProps` 函数一样，获取当前（previous）的 _props_ 值。

在先前的版本中，如果没有特殊的业务需求，实际上是不推荐在 _state_ 中镜像保存一份 _props_ 的状态的——因为你可以随时随地的获取到它，如果做了某些不必要的更改可能会导致数据混乱。在新的版本中，如果你这么做了，在没有特殊业务需求的情况下，不推荐在`getDerivedStateFromProps` 函数外对这个镜像的 _state_ 进行更改。

值得一提的是，在推出`getDerivedStateFromProps` 函数后，除了调用`setState` 函数外，React 有了第二个更新状态的方式。

### 添加事件监听或订阅

这个模式的常见用例是在组件mount时添加外部事件的订阅，并在组件unmount的时候取消订阅。

`componentWillMount` 函数常被用于这类用例。但这么做可能会导致一些问题——因为在初次mount期间任何中断或错误都会导致内存泄漏。（对于没有完成mount的组件，不会调用`componentWillUnmount` 函数，在这种情况下，没有安全的位置来取消订阅。）这种用例下，如果在服务端渲染中使用`componentWillMount` 会造成 context 错误。（因为`componentWillMount` 是不会在服务端被调用的。）

很多人可能认为`componentWillMount` 和`componentWillUnmount` 是一对函数，并且可以成对出现——实际上并不是这样。对于一个组件来说，`componentWillMount` 被调用，不能保证`componentWillUnmount` 也会被调用。

要解决这个问题，我们应该把添加事件监听或订阅的逻辑放到`componentDidMount` 函数中。在正常情况下，组件的`componentDidMount` 被调用后，`componentWillUnmount` 一定也会被调用到。 

#### Before
```js
class ExampleComponent extends React.Component {
  componentWillMount() {
    this.setState({
      subscribedValue: this.props.dataSource.value
    });

    // 不安全！
    this.props.dataSource.subscribe(this._onSubscriptionChange);
  }

  componentWillUnmount() {
    this.props.dataSource.unsubscribe(this._onSubscriptionChange);
  }

  render() {
    // 使用订阅到的数据渲染视图
  }

  _onSubscriptionChange = subscribedValue => {
    this.setState({ subscribedValue });
  };
}
```

#### After
```js
class ExampleComponent extends React.Component {
  state = {
    subscribedValue: this.props.dataSource.value
  };

  componentDidMount() {
    // 必须在mount之后才能安全的添加事件监听，
    // 因此他们不会由于mount期间的中断和错误而导致内存泄漏
    this.props.dataSource.subscribe(this._onSubscriptionChange);

    // 外部值可能在render或mount之前发生改变，
    // 某些状况下，处理这种情况很重要。
    if (this.state.subscribedValue !== this.props.dataSource.value) {
      this.setState({
        subscribedValue: this.props.dataSource.value
      });
    }
  }

  componentWillUnmount() {
    this.props.dataSource.unsubscribe(this._onSubscriptionChange);
  }

  render() {
    // 使用订阅到的数据渲染视图
  }

  _onSubscriptionChange = subscribedValue => {
    this.setState({ subscribedValue });
  };
}
```

### 调用外部函数

这种模式的常见用例是向外部发送一个信号，说明组件内部已经发生了某些变化。

`componentWillMount` 函数常被用与此，但并不能达到理想的效果，因为这类方法可能会被调用多次。为了保证安全性，应当在此处使用`componentDidUpdate` 函数，因为它能保证在每次更新时只被调用一次。（[更多说明](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#invoking-external-callbacks)）

#### Before
```js
class ExampleComponent extends React.Component {
  componentWillUpdate(nextProps, nextState) {
    if (this.state.someStatefulValue !== nextState.someStatefulValue) {
      nextProps.onChange(nextState.someStatefulValue);
    }
  }
}
```

#### After
```js
class ExampleComponent extends React.Component {
  componentDidUpdate(prevProps, prevState) {
    // 回调函数（可能导致副作用）只在（更新）提交后才能被安全执行
    if (this.state.someStatefulValue !== prevState.someStatefulValue) {
      this.props.onChange(this.state.someStatefulValue);
    }
  }
}
```

### _Props_ 改变引发的副作用

这种模式和上面那种有些类似，常见用例是在props改变时执行一些不会改变状态的函数。

与前一种模式不同的是，这种模式用到的是`componentWillReceiveProps` 函数。但与`componentWillUpdate` 函数类似，`componentWillReceiveProps` 可能会在一次更新时被调用多次。为了避免这种副作用，应该在`componentDidUpdate` 中调用，以确保每次更新时只被调用一次。

#### Before
```js
class ExampleComponent extends React.Component {
  componentWillReceiveProps(nextProps) {
    if (this.props.isVisible !== nextProps.isVisible) {
      logVisibleChange(nextProps.isVisible);
    }
  }
}
```

#### After
```js
class ExampleComponent extends React.Component {
  componentDidUpdate(prevProps, prevState) {
    if (this.props.isVisible !== prevProps.isVisible) {
      logVisibleChange(this.props.isVisible);
    }
  }
}
```

### 保存从 _Props_ 或 _State_ 中派生的值

这种模式的用例是保存基于 _`props`_ 或 _`state`_ （也可能是`props`和`state`，原文为 _and/or_ 本段之后的内容不再特别说明） 中计算出来的值。

通常来说这些值会被储存在 _`state`_ 中，但某些情况下可能需要改变它们，因此它们也不适合被称作状态（尽管它们仍应该被存在那里）。这个例子中我们用到了一个外部类来帮助计算和保存我们需要的值，在其他情况下也可以直接从`props` 或`state` 中派生出来。

#### Before
```js
class ExampleComponent extends React.Component {
  componentWillMount() {
    this._calculateMemoizedValues(this.props, this.state);
  }

  componentWillUpdate(nextProps, nextState) {
    if (
      this.props.someValue !== nextProps.someValue ||
      this.state.someOtherValue !== nextState.someOtherValue
    ) {
      this._calculateMemoizedValues(nextProps, nextState);
    }
  }

  render() {
    // 使用计算并保存的值来渲染视图
  }
}
```

#### After
```js
class ExampleComponent extends React.Component {
  render() {
    // 没有被纪录在state中的值可以直接被渲染。
    // 它应该是幂等的，并且没有外部副作用和任何改变。
    this._calculateMemoizedValues(this.props, this.state);

    // 使用计算并保存的值来渲染视图
  }
}
```

