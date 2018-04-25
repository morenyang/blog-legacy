---
title: 在JS中使用修饰器
date: 2018-03-14 10:50:19
tags:
---

在一些面向对象的语言例如Java中，我们经常会使用到修饰器（Decorator）这个东西。
例如：
```java
@RestController
@RequestMapping("/admin")
public class UserController {
	@Autowired
	private UserService userService;
	
	@PermissionRequired(permissionName = "manageUser")
	@GetMapping("/user/list")
	public JSONObject list(@PageableDefault(/*value = 10 DEFAULT*/sort = {"userId"}, direction = Sort.Direction.DESC) Pageable pageable) {
		JSONObject data = new JSONObject();
		Page<User> userList = userService.findAll(pageable);
		data.put(ReturnResult.DATA.getCode(), userList);
		return data;
	}
}
```

学后台的同学看到我的代码就常常向我吐槽JavaScript中怎么就没有这么简便的方法。这几天无意间有看到TS中是支持修饰器的（没错我就是在angular里面看到的），然后研究了一下，发现在JS中也是可以使用它的。

## 开始之前
首先要说明的是，装置器的功能还属于ECMAScript的提案阶段，也就是说他的使用方法可能随时改变，因此使用时需要谨慎考虑。

我们在需要用babel进行翻译，在babel 6+的版本中，可以用插件[`babel-plugin-transform-decorators-legacy`](https://www.npmjs.com/package/babel-plugin-transform-decorators-legacy)。如果是babel 5或更早的版本，可以用`babel-plugin-transform-decorators`或`babel-preset-stage-0`。

实质上，修饰器就是一个方法的语法糖。一般来说，修饰器可以修饰类和类中的方法，使用方式和Java中的修饰器类似。

## 修饰类

这是使用修饰器修饰类的最基础用法：
```ts
function readable(target) {
  target.read = true;
}

@readable
class Book {
  // ...
}

book = new Book();
book.read; // true
```

其中，`@readable`就叫做修饰器，通过`readable`方法劫持了`Book`这个类，并为其加上了`read`属性。其中，`target`这个参数代表被修饰的类。

我们也可以为修饰器指定一些自定义参数，只需要在外面多用一层函数封装即可。
```ts
function readable(isReadable) {
  return function(target) {
    target.readable = isReadable;
  }
}

@readable(ture)
class Book() {
  // ...
}
Book.readable // true

@readable(false)
class Homework() {
  // ...
}
Homework.readable // true
```

但是要注意，上面例子中的用法只能为类添加静态属性，如果你这么调用，他就不管用了：
```js
const book = new Book();
book.readable // undefined
```

因为修饰这个动作实在代码编译的时候完成的，而不是在运行时才进行。如果想要添加实例属性，可以在目标类的`prototype`对象上添加属性，例如：
```ts
function readable(target) {
  target.prototype.read = true;
  target.prototype.readName = function(){
    console.log(this.name)
  };
}

@readable
class Book {
  constructor(){
    this.name = 'laowugui'
  }
}

const book = new Book()
book.read // true
book.readName() // 'laowugui'
```

## 修饰类的方法
与修饰类的修饰器不同，修饰类的方法(属性)的修饰器一般接受三个参数： `target`, `name`, `descriptor`。

其中，`target`是类的原型，指向`target.prototype`；`name`是将要修饰的属性名；`descriptor`是该属性的描述对象。`descriptor`的对象原来的值如下：
```ts
value:Any = specifiedFunction;
enumerable:Bool = false;
configurable:Bool = true;
writable:Bool = true;
```
也就是说，这一作用的修饰器实际上是在修改属性的描述对象(descriptor)。
下面是一个例子：
```ts
function readonly(target, name, descriptor){
  descriptor.writable = false;
  return descriptor;
}

class Book {
  constructor(){
    this.name = 'laowugui'
  }
  
  @readonly
  getName(){
    return `${this.name}`
  }
}
```

如果描述对象中的`enumerable`属性，可以使该属性不能被遍历。
更多例子可以参考[http://es6.ruanyifeng.com/#docs/decorator](http://es6.ruanyifeng.com/#docs/decorator)

另外要说的是，如果一个方法有多个修饰器，那么他们的执行顺序是从外到内（从上往下）的。

##结合React HOC的一些用法
写这篇文章的原因其实有一部分是因为利用修饰器的功能，可以让React的高阶组件（High Order Component）使用得很方便。我们可以将target类封装并返回出一个新的类出来使用。

例如非常常用的`react-redux`组件，正常的声明方法是
```jsx
class Comp extends React.Component{
  render(){
    return (<div>我才不让你报错</div>)
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(MyReactComponent);
```

而如果使用了修饰器，则可以这么写：
```jsx
@connect(mapStateToProps, mapDispatchToProps)(MyReactComponent)
class Comp extends React.Component {
  // ...
}

export default Comp;
```

或者渲染劫持的例子：

```jsx
function authRequire(authName) {
  return function(TargetClass) {
    return class extends TargetClass {
      render(){
        if (userPermissions.has(authName)) {
          return super.render()
        } else {
          return null
        }
      }
    }
  }
}

@authRequire('manage')
class ManageComponent extends React.Component {
  // ...
}
```

## 最后
以上是JS中使用修饰器的一些基础介绍。还是要提醒，使用这些新方法新技术新功能之前一定要慎重。另外，这种使用修饰器的编程方式我们常常称为面向切面编程（AOP）[今天java课老师刚讲 虽然我一句也没听懂]。

#### 参考

[修饰器 - ECMAScript 6入门](http://es6.ruanyifeng.com/#docs/decorator)    
[Decorators for JavaScript](https://github.com/tc39/proposal-decorators)