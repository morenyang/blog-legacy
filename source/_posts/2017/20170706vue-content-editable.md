---
title: 利用ContentEditable属性在VUE下写一个输入框
date: 2017-07-06
tags:
---

上个月好像没更博客，因为期末考比较忙。~~毕竟我这种靠着聪明才智活着的人想要在一群勤奋刻苦的人中间生存下来也不容易。~~

进入正题。今天在写一个文本输入框，我们平时写的能输入文字的标签一般有`input`和`textarea`两种。它们各有不同的属性，`input`只能显示单行文字，而`textarea`虽然可以显示多行，但是它的高度是不能随着文字的长度变化而自适应的——但是我又想要一个足够炫酷的可以自己适应文字高度的文本框。

原本以为只能通过js搞定了，然而刷知乎的时候看到他评论的文本框标签和平时自己看到的很不一样——好了就是他了。
<link href="/static/editorable-placehodler.css" type="text/css" rel="stylesheet">

### contenteditable属性
先来介绍一下这个东西，简单来说，就是可以让你的元素内容是否可编辑。

例如下面这一段文字就可以编辑：

<p contenteditable="true" style="border: 1px solid #eee; padding: 5px 8px">这是一个可编辑的段落。</p>

```html
<p contenteditable>这是一个可编辑的段落。</p>
```

你往里面输入的文字，会被直接写入到网页的HTML代码中。因此如果我们要获取里面的内容，就可以用 `dom.innerText` 来获取。因此当我们打了一段很长的文字时，其实只是相当于在div中加了很长的文字而已。

<div contenteditable="true" placeholder="苟利国家生死以，岂因祸福避趋之">人呐就都不知道，自己就不可以预料。一个人的命运啊，当然要靠自我奋斗，但是也要考虑到历史的行程。我绝对不知道，我作为一个上海市委书记怎么把我选到北京去了，所以邓小平同志跟我讲话，说“中央都决定啦，你来当总书记”，我说另请高明吧。我实在我也不是谦虚，我一个上海市委书记怎么到北京来了呢？但是呢，小平同志讲“大家已经研究决定了”，所以后来我就念了两首诗，叫“苟利国家生死以，岂因祸福避趋之”，那么所以我就到了北京。</div>

我们可以把`input`标签的常用样式套用在`contenteditable`属性的元素里。例如`outline`样式。

#### 实现placeholder

单单一个空白的文本框可能不能满足我们的欲望。肯定有人会想如何给这个文本框加上placeholder。虽然原生不支持placeholder属性，但是我们可以可以用一些奇技淫巧来满足这个要求。

我们按照往常的写法把placeholder属性写进元素里。

```html
<div contenteditable="true" placeholder="添加你的评论"></div>
```

然后通过css来加上placeholder

```css
div[contentEditable]:empty:not(:focus):before {
  content: attr(placeholder);
  color: #aaa;
  font-weight: 300
}
```
效果大概是这样

<div class="input-placeholder" contenteditable="true" placeholder="添加你的评论"></div>

#### 实现maxlength

可能还有一些同学想要顺带实现maxlength的效果，嗯…
~~好好用js解决吧~~ 拖出去续了！

### VUE中实现双向数据绑定

嗯用过vue的人都会常常用到`v-model`这个语法糖。然而只能在`input`、`textarea`、`select`标签和自定义组件中直接使用。（理论上来讲应该是带有value属性的元素）。因此在这里我们要手写一遍数据绑定。

关于`v-model`的介绍，参考使用[自定义事件的表单输入组件](https://cn.vuejs.org/v2/guide/components.html#%E4%BD%BF%E7%94%A8%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6%E7%9A%84%E8%A1%A8%E5%8D%95%E8%BE%93%E5%85%A5%E7%BB%84%E4%BB%B6)
里面给出的解释是

```html
<input v-model="something">
```

等价于

```html
<input v-bind:value="something" v-on:input="something=$event.target.value">
```

因此参照上面的写法，我们也可以自己写一组双向数据绑定:

**html**
```html
<div contenteditable v-text="content" @input="handleInput"></div>
```

**js**
```js
export default{
    data() {
        return{
            content: ''        
        }
    },
    methods: {
        handleInput($event) {
            this.content = $event.target.innerText;
            
            // 下面这段代码可以限制最大长度
            this.content = this.content.substring(0,100);
        }
    }
}
```

当然你也可以把它单独做成一个组件来引用，不过我觉得两者效率差不多。

以上就是用vue实现一个可以自己改变高度的文本框的全部方法介绍啦。过几天就出成绩了希望期末考好点hhhh