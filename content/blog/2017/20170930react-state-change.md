---
title: React笔记:响应状态变化
date: 2017-09-30
tags:
---

最近在上手尝试React框架。其实从Vue切换过来，虽然遇到很多坑，但是并没有想象中的那么困难，组件的整体思路是有一些相似之处的。个人觉得React相比Vue组织起来更严格一些，虽然说写起来比较累，动不动就会给你报个错误，但是也减少了你写出来的代码犯错的概率。

当然相比Vue这种MVVM框架，React这类纯View层框架必须要你自己手动管理数据和状态。<!-- more -->

### Vue中响应状态变化的方法

例如在Vue中会经常用到的`watch`方法，在React中就没有那么方便实现。我们可以利用`watch`方法直接监听`data`（在React中算作`state`）、`props`或是Router中的数据，并对数据的更改做出一些响应。例如：

```js
export default {
  data() {
    return {}
  }
  watch: {
    "$route.params.id"(val){
      this.fetchData()
        .then(res =>{
          // do something
        })
    }
  }
}
```

### React中响应状态变化的方法

#### 响应state变化

而在React中，我们不可能像Vue一样直接拿出`$router`这样的顶级变量。而对于state这类组件中的状态，我们可以直接用`render()`方法对其作出响应：

```js
class App extends React.Component {
  render(){
    if (this.state.number > 1){
      return (<span>+1s</span>)
    } else {
      return (<span>-1s</span>)
    }
  }
}
```

#### 响应props变化

但是对于`props`这类爸爸给我们的数据，我们则需要通过另外一种方法来对他进行响应，这里就需要用到`componentWillReceiveProps()`方法。

`componentWillReceiveProps(nextProps)`属于React的生命周期函数。它的调用仅在收到新的props之后。新的props会被存放在`nextProps`变量里。收到变量以后，你可以对它进行一些处理并把处理后的数据放到`state`里，也可以把`nextProps`和`this.props`进行一番比较，在作出响应。例如：

```js
class App extends React.Component {
  componentWillReceiveProps(nextProps) {
    if(nextProps.speed > this.props.speed) {
      this.setState({speed: nextProps.speed})
    }
  }
  
  render() {
    return (<p>现在香港记者的速度是{this.state.speed}</p>)
  }
}
```

或是你使用React Router的时候，就可以使用这个方法来响应参数的变化：

```js
class App extends React.Component {
  componentWillReceiveProps(nextProps) {
    if (nextProps.match.params.id !== this.props.match.params.id) {
      this.fetchData({id: nextProps.match.params.id})
        .then(res => {
          // do something...
        })
    }
  }
}
```

参考

[https://reactjs.org/docs/react-component.html#componentwillreceiveprops](https://reactjs.org/docs/react-component.html#componentwillreceiveprops)
