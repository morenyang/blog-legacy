---
title: 简单介绍Flex布局
date: 2017-04-19
---

平时我们在网页中使用布局的方法大多基于盒模型，也就是W3C的CSS box model。我们会使用display属性、position属性、float属性，加上margin和padding属性来完成排版。但是这往往是一项较为繁琐的工作，例如垂直居中就比较难实现，或是你想在输入框右边加入一个按钮时，宽度的计算常常让人绝望到崩溃。

然而如果使用Flex布局方式，就可以简便、快捷地实现各种页面的布局。并且FlexBox的兼容性较好，可以说是使用CSS进行网页排版的利器。

### 如果不想看理论，可以直接跳到[例子](#examples)

## Flex布局的框模型
首先要说的是，我们在display属性中声明一个容器flex布局：
```css
.layout {
    display: flex
}
/* 行内元素使用inline-flex*/
.layout-inline {
    display: inline-flex
}
```

简单的说，flex布局容器(**flex container**)中的元素(**flex item**)是基于两条轴线的，横轴为主轴(mian axis)，纵轴为交叉轴（cross axis)。元素默认沿主轴排列。

## Flex Container 的属性
容器主要有几个属性，我们先把常用的放在前面讲。

### justify-content
`justify-content`属性定义了项目在主轴上的对齐方式。有这几个可选项：
```css
.layout {
    display: flex;
    justify-content: flex-start | flex-end | center | space-between | space-around  
}
```

- flex-start : (default) 左对齐
- flex-end : 右对齐
- center : 居中
- space-between : 两端对齐，项目之间的间隔都相等。
- space-around : 每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

效果如下图所示：

![](https://drafts.csswg.org/css-flexbox/images/flex-pack.svg)

### align-items
`align-items`属性定义了项目在交叉轴上的对齐方式。有这几个可选项：

```css
.layout {
    display: flex;
    align-items: flex-start | flex-end | center | baseline | stretch 
}
```

- flex-start : 交叉轴的起点对齐。
- flex-end : 交叉轴的终点对齐。
- center : 交叉轴的中点对齐。
- baseline : 项目的第一行文字的基线对齐。
- stretch : (default) 如果项目未设置高度或设为auto，将占满整个容器的高度。

效果如下图所示：

![](https://drafts.csswg.org/css-flexbox/images/flex-align.svg)

### flex-direction
`flex-direction`属性通过设置flex容器主轴的方向来指定flex项目排列在flex容器中的方式。有这几个可选项：

```css
.layout {
    display: flex;
    flex-direction: row | row-reverse | column | column-reverse
}
```

- row : (defautl) 主轴为水平方向，由左到右。
- row-reverse : 主轴为水平方向，由右到左。
- column : 主轴为垂直方向，由上到下。
- column-reverse : 主轴为垂直方向，由下到上。

效果如下图所示，分别为： `row` `row-reverse` `column` `column-reverse`

![](https://css-tricks.com/wp-content/uploads/2013/04/flex-direction2.svg)

### align-content
`align-content`属性定义了项目在存在多根轴线时的对齐方式。当只有一根轴线时，该属性不起作用。有这几个可选项：
```css
.layout {
    display: flex;
    align-content: flex-start | flex-end | center | space-between | space-around | stretch
}
```

- flex-start : 与交叉轴的起点对齐。
- flex-end : 与交叉轴的终点对齐。
- center : 与交叉轴的中点对齐。
- space-between : 与交叉轴两端对齐，轴线之间的间隔平均分布。
- space-around : 每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
- stretch : (default) 轴线占满整个交叉轴。

效果如下图所示：

![](https://drafts.csswg.org/css-flexbox/images/align-content-example.svg)

### flex-wrap
`flex-wrap`属性控制Flex容器的换行规则。有这几个可选项：

```css
.layout {
    display: flex;
    flex-warp: nowrap | wrap | wrap-reverse
}
```

- nowrap : (default) 不换行
- wrap : 换行，第一行在上方
- wrap-reverse : 换行，第一行在下方

效果如下图所示 ([示例地址](https://developer.mozilla.org/en-US/docs/Web/CSS/flex-wrap))

![](http://morensblog-static.tengtengtengteng.com/img/post/20170419css-flex-box-1.png)

### flex-flow

`flex-flow`属性是`flex-direction` 和 `flex-wrap`属性的简写方式。

```css
.layout {
    display: flex;
    flex-wrap: <flex-direction> || <flex-wrap>
}
```

`flex-flow`的默认属性为： `flex-wrap: row nowrap`

## Flex Items 的属性
Flex 元素中的属性，只影响flex容器中的某单个元素。

### order

`order`定义项目排列的顺序，可以理解成优先级。数字越小，排列越靠前，默认值为0。

```css
.item {
   order: <Integer>
}
```

效果如下图所示：

![](https://css-tricks.com/wp-content/uploads/2013/04/order-2.svg)

[这里有一个小tips](https://drafts.csswg.org/css-flexbox/#order-property)， 你可以使用flex布局来创建一个简单的选项卡，并且将当前选中的选项固定在最前

![](https://drafts.csswg.org/css-flexbox/images/flex-order-example.png)

```css
.tabs {
  display: flex;
}
.tabs > .current {
  order: -1; /* Lower than the default of 0 */
}
```

### align-self

`align-self`属性允许元素定义与其他元素不同的对齐方式。其属性与Flex Container 中的`align-items`基本一致。其中`auto`值为继承父容器的`align-items`属性，如果没有父元素，则为`stretch`。

```css
.flex-item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch 
}
```

具体示例可以参考[这里](https://css-tricks.com/almanac/properties/a/align-self/)

效果如下图所示：
![](https://css-tricks.com/wp-content/uploads/2014/05/align-self.svg)

### flex
`flex`属性其实是由`flex-grow`、`flex-shrink`、`flex-basis`属性组组成的，其中后两者是可选的，默认值为 `flex: 0 1 auto`。这三个属性可以单独制定，但是推荐使用flex属性。

`flex`属性可以指定组件的灵活长度，即由浏览器通过计算推算出其组件的具体长度。

```css
.item {
    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

先来说一下三个子属性

#### flex-grow
flex-grow属性定义元素的放大比例，默认为0，即如果存在剩余空间，也不放大。

```css
.item {
      flex-grow: <number>; /* default 0 */
}
```

[详细说明可以参考](https://css-tricks.com/almanac/properties/f/flex-grow/)

[这里有一个详细的例子](http://codepen.io/HugoGiraudel/pen/9a9ad8fb040f5efaf4e749b49cae7281)

例如，如果所有项目的`flex-grow`属性都为`1`（如下图上方），则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为`2`（如下图下方），其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

![](https://css-tricks.com/wp-content/uploads/2014/05/flex-grow.svg)

#### flex-shrink

与`flex-grow`相反，`flex-shrink属性定义了元素的缩小比例，默认为1，负数无效。如果空间不足，该项目会缩小。

```css
.item {
    flex-shrink: <number>; /* default 1 */
}
```

[详细说明可以参考](https://css-tricks.com/almanac/properties/f/flex-shrink/)

[具体例子](http://codepen.io/HugoGiraudel/pen/95aeaf737efab9c2c1c90ea157c091c6)

举一个例子：如果你不想某个元素在空间不足的时候被缩小，你可以将它的`flex-shrink`属性设置为0。

![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071015.jpg)

#### flex-basis

`flex`属性制定了一个元素的基准长度，即指定浏览器推算当前元素长度前，元素占据主轴的空间。浏览器根据这个属性，计算主轴是否有多余空间。

```css
.item {
    flex-basis: <length> | auto; /* default auto */
}
```

示例如下图：

![](https://drafts.csswg.org/css-flexbox/images/rel-vs-abs-flex.svg)


#### flex

说回到`flex`。`flex`属性有以下这几个常用值。
详细的介绍和例子可以参考 [Basic Values of flex](https://drafts.csswg.org/css-flexbox/#flex-common) 和 [flex | css tricks](https://css-tricks.com/almanac/properties/f/flex/)

##### flex: 0 1 auto

这个是`flex`属性的默认值，也等同于`flex: initial`。它会根据其宽度/高度属性来缩放元素大小。

##### flex: auto

等同于`flex: 1 1 auto`。元素宽度会完全由浏览器推算，从而占据使这个元素整个主轴的剩余空间。

##### flex: none

等同于`flex: 0 0 auto`。元素的高度和宽度会根据属性来渲染，与`flex: initial`类似，但不会被缩小。

##### flex: <positive-number>

等同于`flex: <positive-number> 1 0%`。这允许元素可以占据容器的剩余空间，而且不会被缩小。这个属性最为常用。

##  常用例子
<a name="examples"></a>
<link href="/blog/static/css-flex-box-demo.css" type="text/css" rel="stylesheet">
### 栅格系统

> 可以打开开发人员工具直接查看代码
> *来自[Solved by Flexbox](https://hufan-akari.github.io/solved-by-flexbox/)*


<div class="example-gird-demo"><div class="Grid Grid--gutters u-textCenter"><div class="Grid-cell"><div class="Demo">1/2</div></div><div class="Grid-cell"><div class="Demo">1/2</div></div></div><div class="Grid Grid--gutters u-textCenter"><div class="Grid-cell"><div class="Demo">1/3</div></div><div class="Grid-cell"><div class="Demo">1/3</div></div><div class="Grid-cell"><div class="Demo">1/3</div></div></div><div class="Grid Grid--gutters u-textCenter"><div class="Grid-cell"><div class="Demo">1/4</div></div><div class="Grid-cell"><div class="Demo">1/4</div></div><div class="Grid-cell"><div class="Demo">1/4</div></div><div class="Grid-cell"><div class="Demo">1/4</div></div></div><div class="Grid Grid--gutters Grid--flexCells"><div class="Grid-cell"><div class="Demo">高度撑满，即使内容没有填满空间。</div></div><div class="Grid-cell"><div class="Demo">Here’s to the crazy ones. The misfits. The rebels. The troublemakers. The round pegs in the square holes. The ones who see things differently. </div></div></div></div>

自动栅格参考代码：
```css
.Grid-layout {
  display: flex;
}
.Grid-cell {
    flex: 1;
}
```

#### 垂直居中

> 可以打开开发人员工具直接查看代码
> *来自[Solved by Flexbox](https://hufan-akari.github.io/solved-by-flexbox/)*

<div class="example-gird-demo"><div class="Grid Grid--gutters Grid--center"><div class="Grid-cell"><div class="Demo">我应该相对于我右边的格子垂直居中。</div></div><div class="Grid-cell"><div class="Demo">They’re not fond of rules. And they have no respect for the status quo. You can quote them, disagree with them, glorify or vilify them. About the only thing you can’t do is ignore them. Because they change things. They push the human race forward. And while some may see them as the crazy ones, we see genius. </div></div></div><br></div>

垂直方向居中/置顶/置底参考代码

```css
/* Alignment per row */
.Grid--top {
  align-items: flex-start;
}
.Grid--bottom {
  align-items: flex-end;
}
.Grid--center {
  align-items: center;
}
```

### 输入框组

> 可以打开开发人员工具直接查看代码
> *来自[Solved by Flexbox](https://hufan-akari.github.io/solved-by-flexbox/)*

这个东西在以前真的真的会让人崩溃，计算宽度的时候简直是要人命。现在有了flex布局，实现的方法特别简单。

<div class="example-gird-demo"><div class="Grid Grid--guttersLg Grid--full med-Grid--fit"><div class="Grid-cell"><h4>前置附加组件</h4><div class="InputAddOn"><span class="InputAddOn-item">数量</span> <input class="InputAddOn-field"></div><div class="InputAddOn"><button class="InputAddOn-item">我可以变得很长</button><input class="InputAddOn-field"></div></div><div class="Grid-cell"><h4>后置附加组件</h4><div class="InputAddOn"><input class="InputAddOn-field" placeholder="来不及解释了"> <button class="InputAddOn-item">快上车！</button></div><div class="InputAddOn"><input class="InputAddOn-field"> <div class="InputAddOn-item">@yangteng.me</div></div></div></div></div>

<div class="example-gird-demo"><h4>前置附加组件和后置附加组件</h4><div class="Grid Grid--guttersLg Grid--full med-Grid--fit"><div class="Grid-cell"><div class="InputAddOn"><span class="InputAddOn-item">老司机</span> <input class="InputAddOn-field"> <button class="InputAddOn-item">带带我</button></div></div></div></div>

HTML代码

```html
<!-- appending -->
<div class="input-group">
  <input class="input-field">
  <button class="add-item">…</button>
</div>
<!-- prepending -->
<div class="input-group">
  <span class="add-item">…</span>
  <input class="input-field">
</div>
<!-- both -->
<div class="input-group">
  <span class="InputAddOn-item">…</span>
  <input class="input-field">
  <button class="add-item">…</button>
</div>
```

CSS代码

```css
.input-group {
  display: flex;
}
.input-field {
  flex: 1;
}
```

### 居中显示

> 可以打开开发人员工具直接查看代码
> *来自[Solved by Flexbox](https://hufan-akari.github.io/solved-by-flexbox/)*

垂直居中一直缺少较好而又简便的方法。而在flex布局中，我们可以使用`align-items`, `align-self`, 和 `justify-content` 这些属性来控制元素在容器中居中显示。

<div class="example-gird-demo"><div class="Demo Demo--spaced u-ieMinHeightBugFix"><div class="Aligner"><div class="Aligner-item Aligner-item--fixed"><div class="Demo"><h3>我居中啦!</h3><p contenteditable="true">Because the people who are crazy enough to think they can change the world are the ones who do.</p></div></div></div></div></div>

示例代码

```css
.layout{
  display: flex;
  align-items: center;
  justify-content: center;
}
```

### 粘性页脚

> *来自[Solved by Flexbox](https://hufan-akari.github.io/solved-by-flexbox/)*

使用传统的CSS布局方式，如果不加以JS辅助，是无法完美解决这个问题的。现在有了Flex布局，我们完美终于可以完美的放置页脚了。

html代码

```html
<body>
    <header></header>
    <main></main>
    <footer></footer>
</body>
```

css代码

```css
body{
  display: flex;
  min-height: 100vh;
  flex-direction: column;
}
main{
  flex: 1;
}
```

### 流式布局


类似于Bootstrap栅格系统中，每行固定数量排列，如果项目数超出固定数量后会自动换行。

> *来自[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)*

<div class="example-gird-demo"><div class="Grid Grid--gutters u-textCenter"><div class="Grid-cell u-1of4"><div class="Demo">1</div></div><div class="Grid-cell u-1of4"><div class="Demo">1</div></div><div class="Grid-cell u-1of4"><div class="Demo">1</div></div><div class="Grid-cell u-1of4"><div class="Demo">1</div></div><div class="Grid-cell u-1of4"><div class="Demo">1</div></div><div class="Grid-cell u-1of2"><div class="Demo">2</div></div></div></div>

css代码

```css
.parent {
  width: 200px;
  height: 150px;
  background-color: black;
  display: flex;
  flex-flow: row wrap;
  align-content: flex-start;
}
.child {
  box-sizing: border-box;
  background-color: white;
  flex: 0 0 25%;
  height: 50px;
  border: 1px solid red;
}
```

### 其他

还有一些布局方式，这里就不一一举例了，如果有想了解的可以看一下下面的例子：

#### 九宫格式布局

[Getting Dicey With Flexbox](https://davidwalsh.name/flexbox-dice)

#### 圣杯式布局

[圣杯布局](https://hufan-akari.github.io/solved-by-flexbox/demos/holy-grail/)

#### 媒体对象布局

<div class="example-gird-demo"><div class="Demo Demo--spaced"><div class="Media"><img class="Media-figure Image" src="//hufan-akari.github.io/solved-by-flexbox/images/kitten.jpg" alt="Kitten"><div class="Media-body"><h3 class="Media-title">Think Different. </h3><p>Here’s to the crazy ones. The misfits. The rebels. The troublemakers. The round pegs in the square holes. The ones who see things differently. They’re not fond of rules. And they have no respect for the status quo. You can quote them, disagree with them, glorify or vilify them. About the only thing you can’t do is ignore them. Because they change things. They push the human race forward. And while some may see them as the crazy ones, we see genius. Because the people who are crazy enough to think they can change the world are the ones who do.</p></div></div></div></div>

[媒体对象](https://hufan-akari.github.io/solved-by-flexbox/demos/media-object/)

-----

嗯这篇文章挺长的，而且很多内容都是参考别人写的东西。不过也挺好的，起码自己代码敲一遍，内容差不多都消化完了。

嘻嘻。再见

参考 [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)、[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html) 、官方文档 [CSS Flexible Box Layout Module Level 1](https://drafts.csswg.org/css-flexbox/)、[A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)、以及[Solved by Flexbox](https://hufan-akari.github.io/solved-by-flexbox/)

