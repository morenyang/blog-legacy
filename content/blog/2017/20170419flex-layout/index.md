---
title: 简单介绍Flex布局
date: 2017-04-19
---

平时我们在网页中使用布局的方法大多基于盒模型，也就是W3C的CSS box model。我们会使用display属性、position属性、float属性，加上margin和padding属性来完成排版。但是这往往是一项较为繁琐的工作，例如垂直居中就比较难实现，或是你想在输入框右边加入一个按钮时，宽度的计算常常让人绝望到崩溃。

然而如果使用Flex布局方式，就可以简便、快捷地实现各种页面的布局。并且FlexBox的兼容性较好，可以说是使用CSS进行网页排版的利器。<!-- more -->

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
### 栅格系统

> 可以打开开发人员工具直接查看代码
> *来自[Solved by Flexbox](https://magic-akari.github.io/solved-by-flexbox/)*


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
> *来自[Solved by Flexbox](https://magic-akari.github.io/solved-by-flexbox/)*

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
> *来自[Solved by Flexbox](https://magic-akari.github.io/solved-by-flexbox/)*

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
> *来自[Solved by Flexbox](https://magic-akari.github.io/solved-by-flexbox/)*

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

> *来自[Solved by Flexbox](https://magic-akari.github.io/solved-by-flexbox/)*

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

[圣杯布局](https://magic-akari.github.io/solved-by-flexbox/demos/holy-grail/)

#### 媒体对象布局

<div class="example-gird-demo"><div class="Demo Demo--spaced"><div class="Media"><img class="Media-figure Image" src="https://magic-akari.github.io/solved-by-flexbox/images/kitten.jpg" alt="Kitten"><div class="Media-body"><h3 class="Media-title">Think Different. </h3><p>Here’s to the crazy ones. The misfits. The rebels. The troublemakers. The round pegs in the square holes. The ones who see things differently. They’re not fond of rules. And they have no respect for the status quo. You can quote them, disagree with them, glorify or vilify them. About the only thing you can’t do is ignore them. Because they change things. They push the human race forward. And while some may see them as the crazy ones, we see genius. Because the people who are crazy enough to think they can change the world are the ones who do.</p></div></div></div></div>

[媒体对象](https://magic-akari.github.io/solved-by-flexbox/demos/media-object/)

-----

嗯这篇文章挺长的，而且很多内容都是参考别人写的东西。不过也挺好的，起码自己代码敲一遍，内容差不多都消化完了。

嘻嘻。再见

参考 [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)、[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html) 、官方文档 [CSS Flexible Box Layout Module Level 1](https://drafts.csswg.org/css-flexbox/)、[A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)、以及[Solved by Flexbox](https://magic-akari.github.io/solved-by-flexbox/)


<style>
.example-gird-demo html {
	font-family: sans-serif;
	-ms-text-size-adjust: 100%;
	-webkit-text-size-adjust: 100%
}

.example-gird-demo body {
	margin: 0
}

.example-gird-demo article,.example-gird-demo aside,.example-gird-demo details,.example-gird-demo figcaption,.example-gird-demo figure,.example-gird-demo footer,.example-gird-demo header,.example-gird-demo hgroup,.example-gird-demo main,.example-gird-demo menu,.example-gird-demo nav,.example-gird-demo section,.example-gird-demo summary {
	display: block
}

.example-gird-demo audio,.example-gird-demo canvas,.example-gird-demo progress,.example-gird-demo video {
	display: inline-block;
	vertical-align: baseline
}

.example-gird-demo audio:not([controls]) {
	display: none;
	height: 0
}

.example-gird-demo [hidden],.example-gird-demo template {
	display: none
}

.example-gird-demo a {
	background-color: transparent
}

.example-gird-demo a:active,.example-gird-demo a:hover {
	outline: 0
}

.example-gird-demo abbr[title] {
	border-bottom: 1px dotted
}

.example-gird-demo b,.example-gird-demo strong {
	font-weight: 700
}

.example-gird-demo dfn {
	font-style: italic
}

.example-gird-demo h1 {
	margin: .67em 0;
	font-size: 2em
}

.example-gird-demo mark {
	background: #ff0;
	color: #000
}

.example-gird-demo small {
	font-size: 80%
}

.example-gird-demo sub,.example-gird-demo sup {
	position: relative;
	vertical-align: baseline;
	font-size: 75%;
	line-height: 0
}

.example-gird-demo sup {
	top: -.5em
}

.example-gird-demo sub {
	bottom: -.25em
}

.example-gird-demo img {
	border: 0
}

.example-gird-demo svg:not(:root) {
	overflow: hidden
}

.example-gird-demo figure {
	margin: 1em 40px
}

.example-gird-demo hr {
	box-sizing: content-box;
	height: 0
}

.example-gird-demo pre {
	overflow: auto
}

.example-gird-demo code,.example-gird-demo kbd,.example-gird-demo pre,.example-gird-demo samp {
	font-size: 1em;
	font-family: monospace
}

.example-gird-demo button,.example-gird-demo input,.example-gird-demo optgroup,.example-gird-demo select,.example-gird-demo textarea {
	margin: 0;
	color: inherit;
	font: inherit
}

.example-gird-demo button {
	overflow: visible
}

.example-gird-demo button,.example-gird-demo select {
	text-transform: none
}

.example-gird-demo button,.example-gird-demo html input[type=button],.example-gird-demo input[type=reset],.example-gird-demo input[type=submit] {
	cursor: pointer;
	-webkit-appearance: button
}

.example-gird-demo button[disabled],.example-gird-demo html input[disabled] {
	cursor: default
}

.example-gird-demo button::-moz-focus-inner,.example-gird-demo input::-moz-focus-inner {
	padding: 0;
	border: 0
}

.example-gird-demo input {
	line-height: normal
}

.example-gird-demo input[type=checkbox],.example-gird-demo input[type=radio] {
	box-sizing: border-box;
	padding: 0
}

.example-gird-demo input[type=number]::-webkit-inner-spin-button,.example-gird-demo input[type=number]::-webkit-outer-spin-button {
	height: auto
}

.example-gird-demo input[type=search] {
	box-sizing: content-box;
	-webkit-appearance: textfield
}

.example-gird-demo input[type=search]::-webkit-search-cancel-button,.example-gird-demo input[type=search]::-webkit-search-decoration {
	-webkit-appearance: none
}

.example-gird-demo fieldset {
	margin: 0 2px;
	padding: .35em .625em .75em;
	border: 1px solid silver
}

.example-gird-demo legend {
	padding: 0;
	border: 0
}

.example-gird-demo textarea {
	overflow: auto
}

.example-gird-demo optgroup {
	font-weight: 700
}

.example-gird-demo table {
	border-collapse: collapse;
	border-spacing: 0
}

.example-gird-demo td,.example-gird-demo th {
	padding: 0
}

.example-gird-demo .u-block {
	display: block!important
}

.example-gird-demo .u-hidden {
	display: none!important
}

.example-gird-demo .u-hiddenVisually {
	position: absolute!important;
	overflow: hidden!important;
	clip: rect(1px,1px,1px,1px)!important;
	padding: 0!important;
	width: 1px!important;
	height: 1px!important;
	border: 0!important
}

.example-gird-demo .u-inline {
	display: inline!important
}

.example-gird-demo .u-inlineBlock {
	display: inline-block!important;
	max-width: 100%
}

.example-gird-demo .u-table {
	display: table!important
}

.example-gird-demo .u-tableCell {
	display: table-cell!important
}

.example-gird-demo .u-tableRow {
	display: table-row!important
}

.example-gird-demo .u-textBreak {
	word-wrap: break-word!important
}

.example-gird-demo .u-textCenter {
	text-align: center!important
}

.example-gird-demo .u-textLeft {
	text-align: left!important
}

.example-gird-demo .u-textRight {
	text-align: right!important
}

.example-gird-demo .u-textInheritColor {
	color: inherit!important
}

.example-gird-demo .u-textKern {
	text-rendering: optimizeLegibility;
	-webkit-font-feature-settings: "kern" 1,"kern";
	-moz-font-feature-settings: "kern" 1,"kern";
	font-feature-settings: kern\ 1,kern;
	-webkit-font-kerning: normal;
	-moz-font-kerning: normal;
	font-kerning: normal
}

.example-gird-demo .u-textNoWrap,.example-gird-demo .u-textTruncate {
	white-space: nowrap!important
}

.example-gird-demo .u-textTruncate {
	overflow: hidden!important;
	max-width: 100%;
	text-overflow: ellipsis!important;
	word-wrap: normal!important
}

.example-gird-demo *,.example-gird-demo :after,.example-gird-demo :before {
	box-sizing: border-box
}

.example-gird-demo html {
	height: 100%;
	color: #404040;
	text-align: justify;
	text-justify: inter-ideograph;
	font: 400 1em/1.4 -apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Helvetica,Arial,sans-serif;
	text-rendering: optimizeLegibility;
	-ms-text-justify: inter-ideograph;
	-moz-text-align-last: justify;
	-webkit-text-align-last: justify
}

.example-gird-demo h1 {
	margin: .25em 0 .75em;
	letter-spacing: -.015em;
	font-weight: 300;
	font-size: 2em;
	line-height: 1;
	-webkit-font-kerning: normal
}

@media (min-width:768px) {
	.example-gird-demo h1 {
		margin: .5em 0 1em;
		font-size: 2.5em
	}
}

.example-gird-demo h2 {
	margin: 0 0 1.12528em;
	font-size: 1.333em
}

.example-gird-demo h2,.example-gird-demo h3 {
	font-weight: 600
}

.example-gird-demo h3 {
	font-size: 1em
}

.example-gird-demo h3,.example-gird-demo p,.example-gird-demo pre {
	margin: 0 0 1.5em
}

.example-gird-demo code,.example-gird-demo pre {
	font-family: Consolas,Liberation Mono,Menlo,Courier,monospace
}

.example-gird-demo code {
	color: #000;
	font-weight: 400;
	font-size: .9em
}

.example-gird-demo pre>code {
	color: inherit;
	font: inherit
}

.example-gird-demo a {
	border-bottom: 1px dashed rgba(70,185,128,.5);
	color: #46b980;
	text-decoration: none
}

.example-gird-demo a:focus,.example-gird-demo a:hover {
	border-bottom: 1px solid #46b980
}

.example-gird-demo ol,.example-gird-demo ul {
	margin: 0 0 1.5em;
	padding: 0 0 0 1.5em;
	list-style: square
}

.example-gird-demo li {
	margin-bottom: .333em
}

.example-gird-demo figure {
	margin: 0
}

.example-gird-demo strong {
	font-weight: 600
}

.example-gird-demo .Aligner {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex;
	min-height: 24em;
	-webkit-box-align: center;
	-ms-flex-align: center;
	align-items: center;
	-webkit-box-pack: center;
	-ms-flex-pack: center;
	justify-content: center
}

.example-gird-demo .Aligner-item {
	-webkit-box-flex: 1;
	-ms-flex: 1;
	flex: 1
}

.example-gird-demo .Aligner-item--top {
	-ms-flex-item-align: start;
	align-self: flex-start
}

.example-gird-demo .Aligner-item--bottom {
	-ms-flex-item-align: end;
	align-self: flex-end
}

.example-gird-demo .Aligner-item--fixed {
	max-width: 50%;
	-webkit-box-flex: 0;
	-ms-flex: none;
	flex: none
}

.example-gird-demo .Browser {
	text-align: center;
	font-size: .8em
}

.example-gird-demo .Browser-image {
	margin: 0 .5em .5em;
	width: 4pc;
	height: 4pc;
	background: url(images/browser-logos.jpg) no-repeat 0 0;
	background-size: auto 100%
}

.example-gird-demo .Browser--chrome>.Browser-image {
	background-position: 0 0
}

.example-gird-demo .Browser--opera>.Browser-image {
	background-position: -4pc 0
}

.example-gird-demo .Browser--firefox>.Browser-image {
	background-position: -8pc 0
}

.example-gird-demo .Browser--safari>.Browser-image {
	background-position: -2in 0
}

.example-gird-demo .Browser--ie>.Browser-image {
	background-position: -16pc 0
}

.example-gird-demo .Browser--edge>.Browser-image {
	background-position: -20pc 0
}

.example-gird-demo .Button {
	display: inline-block;
	padding: .6em 1em;
	border: 0;
	border-radius: 2px;
	background: hsla(31,15%,50%,.15);
	color: inherit;
	text-decoration: none;
	white-space: nowrap;
	font-weight: 300;
	font-size: .8125em;
	line-height: normal;
	cursor: pointer;
	-webkit-transition: background-color .2s;
	transition: background-color .2s
}

.example-gird-demo .Button:focus {
	outline: thin dotted #666;
	text-decoration: none
}

.example-gird-demo .Button:active,.example-gird-demo .Button:focus,.example-gird-demo .Button:hover {
	border: 0;
	background: hsla(31,15%,50%,.25);
	text-decoration: none
}

.example-gird-demo .Button--action {
	background-color: #46b980;
	color: #fff
}

.example-gird-demo .Button--action:active,.example-gird-demo .Button--action:focus,.example-gird-demo .Button--action:hover {
	background-color: #389466
}

.example-gird-demo .Button--wide {
	padding-right: 1.5em;
	padding-left: 1.5em
}

.example-gird-demo .Container {
	margin: 0 auto;
	max-width: 50em
}

.example-gird-demo .Demo {
	padding: .8em 1em 0;
	width: 100%;
	border-radius: 3px;
	background: hsla(31,15%,50%,.1)
}

.example-gird-demo .Demo:after {
	display: block;
	visibility: hidden;
	margin-top: 1em;
	height: 0;
	content: '\00a0'
}

.example-gird-demo .Demo--spaced {
	margin-bottom: 1.5em
}

.example-gird-demo .Error {
	padding: 1em 1.5em;
	background: #c00;
	color: #fff;
	text-align: center;
	font-weight: 700
}

.example-gird-demo .Feature-figure {
	margin-bottom: .75em;
	border: 1px solid #d9d9d9;
	-webkit-transition: border-color .2s;
	transition: border-color .2s
}

.example-gird-demo .Feature-image {
	display: block;
	height: auto;
	max-width: 100%;
	border: 5px solid #fff
}

.example-gird-demo .Feature-title {
	margin: 0 0 .5em;
	color: #404040;
	text-align: center;
	-webkit-transition: color .1s;
	transition: color .1s
}

.example-gird-demo .Feature-description {
	margin: 0 .75em;
	font-size: .8em
}

.example-gird-demo .Feature a:active .Feature-figure,.example-gird-demo .Feature a:focus .Feature-figure,.example-gird-demo .Feature a:hover .Feature-figure {
	border-color: #46b980
}

.example-gird-demo .Feature a:active .Feature-title,.example-gird-demo .Feature a:focus .Feature-title,.example-gird-demo .Feature a:hover .Feature-title {
	color: #46b980
}

.example-gird-demo .Footer {
	padding: 24px;
	padding: 1.5rem;
	background: #404040;
	color: #999;
	text-align: center;
	font-size: .85em
}

.example-gird-demo .Footer a {
	padding-bottom: 1px;
	border: 0;
	color: #e5e5e5
}

.example-gird-demo .Footer a:active,.example-gird-demo .Footer a:focus,.example-gird-demo .Footer a:hover {
	color: #fff;
	text-decoration: underline
}

.example-gird-demo .Footer-credits {
	margin: 0;
	padding: 0
}

.example-gird-demo .Footer-credit {
	display: block;
	margin: 0
}

.example-gird-demo .Footer-creditSeparator {
	display: none
}

.example-gird-demo .Footer-social a,.example-gird-demo .Footer-social iframe {
	display: inline-block;
	margin: 0 0 1em;
	vertical-align: top
}

@media (min-width:576px) {
	.example-gird-demo .Footer-credit {
		display: inline-block;
		margin: 0 .25em
	}

	.example-gird-demo .Footer-creditSeparator {
		display: inline-block;
		padding: 0 .25em
	}
}

.example-gird-demo .Grid {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex;
	margin: 0;
	padding: 0;
	list-style: none;
	-ms-flex-wrap: wrap;
	flex-wrap: wrap
}

.example-gird-demo .Grid-cell {
	-webkit-box-flex: 1;
	-ms-flex: 1;
	flex: 1
}

.example-gird-demo .Grid--flexCells>.Grid-cell {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex
}

.example-gird-demo .Grid--top {
	-webkit-box-align: start;
	-ms-flex-align: start;
	align-items: flex-start
}

.example-gird-demo .Grid--bottom {
	-webkit-box-align: end;
	-ms-flex-align: end;
	align-items: flex-end
}

.example-gird-demo .Grid--center {
	-webkit-box-align: center;
	-ms-flex-align: center;
	align-items: center
}

.example-gird-demo .Grid--justifyCenter {
	-webkit-box-pack: center;
	-ms-flex-pack: center;
	justify-content: center
}

.example-gird-demo .Grid-cell--top {
	-ms-flex-item-align: start;
	align-self: flex-start
}

.example-gird-demo .Grid-cell--bottom {
	-ms-flex-item-align: end;
	align-self: flex-end
}

.example-gird-demo .Grid-cell--center {
	-ms-flex-item-align: center;
	align-self: center
}

.example-gird-demo .Grid-cell--autoSize {
	-webkit-box-flex: 0;
	-ms-flex: none;
	flex: none
}

.example-gird-demo .Grid--fit>.Grid-cell {
	-webkit-box-flex: 1;
	-ms-flex: 1;
	flex: 1
}

.example-gird-demo .Grid--full>.Grid-cell {
	-webkit-box-flex: 0;
	-ms-flex: 0 0 100%;
	flex: 0 0 100%
}

.example-gird-demo .Grid--1of2>.Grid-cell {
	-webkit-box-flex: 0;
	-ms-flex: 0 0 50%;
	flex: 0 0 50%
}

.example-gird-demo .Grid--1of3>.Grid-cell {
	-webkit-box-flex: 0;
	-ms-flex: 0 0 33.3333%;
	flex: 0 0 33.3333%
}

.example-gird-demo .Grid--1of4>.Grid-cell {
	-webkit-box-flex: 0;
	-ms-flex: 0 0 25%;
	flex: 0 0 25%
}

@media (min-width:384px) {
	.example-gird-demo .small-Grid--fit>.Grid-cell {
		-webkit-box-flex: 1;
		-ms-flex: 1;
		flex: 1
	}

	.example-gird-demo .small-Grid--full>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 100%;
		flex: 0 0 100%
	}

	.example-gird-demo .small-Grid--1of2>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 50%;
		flex: 0 0 50%
	}

	.example-gird-demo .small-Grid--1of3>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 33.3333%;
		flex: 0 0 33.3333%
	}

	.example-gird-demo .small-Grid--1of4>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 25%;
		flex: 0 0 25%
	}
}

@media (min-width:576px) {
	.example-gird-demo .med-Grid--fit>.Grid-cell {
		-webkit-box-flex: 1;
		-ms-flex: 1;
		flex: 1
	}

	.example-gird-demo .med-Grid--full>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 100%;
		flex: 0 0 100%
	}

	.example-gird-demo .med-Grid--1of2>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 50%;
		flex: 0 0 50%
	}

	.example-gird-demo .med-Grid--1of3>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 33.3333%;
		flex: 0 0 33.3333%
	}

	.example-gird-demo .med-Grid--1of4>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 25%;
		flex: 0 0 25%
	}
}

@media (min-width:768px) {
	.example-gird-demo .large-Grid--fit>.Grid-cell {
		-webkit-box-flex: 1;
		-ms-flex: 1;
		flex: 1
	}

	.example-gird-demo .large-Grid--full>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 100%;
		flex: 0 0 100%
	}

	.example-gird-demo .large-Grid--1of2>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 50%;
		flex: 0 0 50%
	}

	.example-gird-demo .large-Grid--1of3>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 33.3333%;
		flex: 0 0 33.3333%
	}

	.example-gird-demo .large-Grid--1of4>.Grid-cell {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 25%;
		flex: 0 0 25%
	}
}

.example-gird-demo .Grid--gutters {
	margin: -1em 0 1em -1em
}

.example-gird-demo .Grid--gutters>.Grid-cell {
	padding: 1em 0 0 1em
}

.example-gird-demo .Grid--guttersLg {
	margin: -1.5em 0 1.5em -1.5em
}

.example-gird-demo .Grid--guttersLg>.Grid-cell {
	padding: 1.5em 0 0 1.5em
}

.example-gird-demo .Grid--guttersXl {
	margin: -2em 0 2em -2em
}

.example-gird-demo .Grid--guttersXl>.Grid-cell {
	padding: 2em 0 0 2em
}

@media (min-width:384px) {
	.example-gird-demo .small-Grid--gutters {
		margin: -1em 0 1em -1em
	}

	.example-gird-demo .small-Grid--gutters>.Grid-cell {
		padding: 1em 0 0 1em
	}

	.example-gird-demo .small-Grid--guttersLg {
		margin: -1.5em 0 1.5em -1.5em
	}

	.example-gird-demo .small-Grid--guttersLg>.Grid-cell {
		padding: 1.5em 0 0 1.5em
	}

	.example-gird-demo .small-Grid--guttersXl {
		margin: -2em 0 2em -2em
	}

	.example-gird-demo .small-Grid--guttersXl>.Grid-cell {
		padding: 2em 0 0 2em
	}
}

@media (min-width:576px) {
	.example-gird-demo .med-Grid--gutters {
		margin: -1em 0 1em -1em
	}

	.example-gird-demo .med-Grid--gutters>.Grid-cell {
		padding: 1em 0 0 1em
	}

	.example-gird-demo .med-Grid--guttersLg {
		margin: -1.5em 0 1.5em -1.5em
	}

	.example-gird-demo .med-Grid--guttersLg>.Grid-cell {
		padding: 1.5em 0 0 1.5em
	}

	.example-gird-demo .med-Grid--guttersXl {
		margin: -2em 0 2em -2em
	}

	.example-gird-demo .med-Grid--guttersXl>.Grid-cell {
		padding: 2em 0 0 2em
	}
}

@media (min-width:768px) {
	.example-gird-demo .large-Grid--gutters {
		margin: -1em 0 1em -1em
	}

	.example-gird-demo .large-Grid--gutters>.Grid-cell {
		padding: 1em 0 0 1em
	}

	.example-gird-demo .large-Grid--guttersLg {
		margin: -1.5em 0 1.5em -1.5em
	}

	.example-gird-demo .large-Grid--guttersLg>.Grid-cell {
		padding: 1.5em 0 0 1.5em
	}

	.example-gird-demo .large-Grid--guttersXl {
		margin: -2em 0 2em -2em
	}

	.example-gird-demo .large-Grid--guttersXl>.Grid-cell {
		padding: 2em 0 0 2em
	}
}

.example-gird-demo .Header {
	padding: 1.5em;
	background-color: hsla(31,15%,50%,.1);
	text-align: center
}

@media (min-width:768px) {
	.example-gird-demo .Header {
		padding: 3em 1.5em
	}
}

.example-gird-demo .Header-title {
	margin: 0 0 .15em;
	word-spacing: .08em;
	font-weight: 600;
	font-size: 1.8em;
	line-height: 1
}

.example-gird-demo .Header-title i {
	font-weight: 400;
	font-style: italic;
	font-family: serif
}

.example-gird-demo .Header-title a {
	border: 0;
	color: inherit;
	font-weight: inherit
}

.example-gird-demo .Header-title a:active,.example-gird-demo .Header-title a:focus,.example-gird-demo .Header-title a:hover {
	text-decoration: none
}

@media (min-width:768px) {
	.example-gird-demo .Header-title {
		font-size: 4em
	}
}

.example-gird-demo .Header-subTitle {
	margin: 0 0 1.5em;
	white-space: nowrap;
	font-weight: 300;
	font-size: .8em
}

@media (min-width:768px) {
	.example-gird-demo .Header-subTitle {
		margin: 1em 0 1.75em;
		font-size: 1.1em
	}
}

.example-gird-demo .Header-actions {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex;
	font-size: .9em;
	-webkit-box-align: stretch;
	-ms-flex-align: stretch;
	align-items: stretch;
	-webkit-box-orient: vertical;
	-webkit-box-direction: normal;
	-ms-flex-direction: column;
	flex-direction: column
}

@media (min-width:384px) {
	.example-gird-demo .Header-actions {
		-webkit-box-align: center;
		-ms-flex-align: center;
		align-items: center;
		-webkit-box-orient: horizontal;
		-webkit-box-direction: normal;
		-ms-flex-direction: row;
		flex-direction: row;
		-webkit-box-pack: center;
		-ms-flex-pack: center;
		justify-content: center
	}
}

@media (min-width:768px) {
	.example-gird-demo .Header-actions {
		font-size: 1.1em
	}
}

.example-gird-demo .Header-button:first-child {
	margin: 0 0 1em
}

@media (min-width:384px) {
	.example-gird-demo .Header-button:first-child {
		margin: 0 1em 0 0
	}
}

@media (min-width:768px) {
	.example-gird-demo .Header--cozy {
		padding: 1.5em;
		-webkit-box-align: center;
		-ms-flex-align: center;
		align-items: center
	}

	.example-gird-demo .Header--cozy,.example-gird-demo .Header--cozy .Header-titles {
		display: -webkit-box;
		display: -ms-flexbox;
		display: flex
	}

	.example-gird-demo .Header--cozy .Header-titles {
		-webkit-box-align: baseline;
		-ms-flex-align: baseline;
		align-items: baseline
	}

	.example-gird-demo .Header--cozy .Header-title {
		font-size: 1.5em
	}

	.example-gird-demo .Header--cozy .Header-subTitle {
		margin: 0 0 0 1em;
		color: grey;
		font-weight: 300;
		font-size: .8em
	}

	.example-gird-demo .Header--cozy .Header-actions {
		font-size: .9em;
		-webkit-box-flex: 1;
		-ms-flex: 1;
		flex: 1;
		-webkit-box-pack: end;
		-ms-flex-pack: end;
		justify-content: flex-end
	}
}

.example-gird-demo .HolyGrail {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex;
	height: 100%;
	-webkit-box-orient: vertical;
	-webkit-box-direction: normal;
	-ms-flex-direction: column;
	flex-direction: column
}

.example-gird-demo .HolyGrail-footer,.example-gird-demo .HolyGrail-header {
	-webkit-box-flex: 0;
	-ms-flex: none;
	flex: none
}

.example-gird-demo .HolyGrail-body {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex;
	padding: 1.5em;
	-webkit-box-flex: 1;
	-ms-flex: 1 0 auto;
	flex: 1 0 auto;
	-webkit-box-orient: vertical;
	-webkit-box-direction: normal;
	-ms-flex-direction: column;
	flex-direction: column
}

.example-gird-demo .HolyGrail-content {
	margin-top: 1.5em
}

.example-gird-demo .HolyGrail-nav {
	-webkit-box-ordinal-group: 0;
	-ms-flex-order: -1;
	order: -1
}

.example-gird-demo .HolyGrail-ads,.example-gird-demo .HolyGrail-nav {
	padding: 1em;
	border-radius: 3px;
	background: hsla(31,15%,50%,.1)
}

@media (min-width:768px) {
	.example-gird-demo .HolyGrail-body {
		-webkit-box-orient: horizontal;
		-webkit-box-direction: normal;
		-ms-flex-direction: row;
		flex-direction: row
	}

	.example-gird-demo .HolyGrail-content {
		margin: 0;
		padding: 0 2em;
		-webkit-box-flex: 1;
		-ms-flex: 1;
		flex: 1
	}

	.example-gird-demo .HolyGrail-ads,.example-gird-demo .HolyGrail-nav {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 12em;
		flex: 0 0 12em
	}
}

.example-gird-demo .Image {
	display: block;
	margin-top: .2em;
	width: 40px;
	height: auto
}

.example-gird-demo .Image--tiny {
	width: 30px
}

@media (min-width:576px) {
	.example-gird-demo .Image {
		width: 70px
	}

	.example-gird-demo .Image--tiny {
		width: 40px
	}
}

.example-gird-demo .InputAddOn {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex;
	margin-bottom: 1.5em
}

.example-gird-demo .InputAddOn-field {
	-webkit-box-flex: 1;
	-ms-flex: 1;
	flex: 1
}

.example-gird-demo .InputAddOn-field:not(:first-child) {
	border-left: 0
}

.example-gird-demo .InputAddOn-field:not(:last-child) {
	border-right: 0
}

.example-gird-demo .InputAddOn-item {
	background-color: hsla(31,15%,50%,.1);
	color: #666;
	font: inherit;
	font-weight: 400
}

.example-gird-demo .InputAddOn-field,.example-gird-demo .InputAddOn-item {
	padding: .5em .75em;
	border: 1px solid hsla(31,15%,50%,.25)
}

.example-gird-demo .InputAddOn-field:first-child,.example-gird-demo .InputAddOn-item:first-child {
	border-radius: 2px 0 0 2px
}

.example-gird-demo .InputAddOn-field:last-child,.example-gird-demo .InputAddOn-item:last-child {
	border-radius: 0 2px 2px 0
}

.example-gird-demo .Media {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex;
	margin-bottom: 1em;
	-webkit-box-align: start;
	-ms-flex-align: start;
	align-items: flex-start
}

.example-gird-demo .Media-figure {
	margin-right: 1em
}

.example-gird-demo .Media-body {
	-webkit-box-flex: 1;
	-ms-flex: 1;
	flex: 1
}

.example-gird-demo .Media-body,.example-gird-demo .Media-body :last-child {
	margin-bottom: 0
}

.example-gird-demo .Media-title {
	margin: 0 0 .5em
}

.example-gird-demo .Media--center {
	-webkit-box-align: center;
	-ms-flex-align: center;
	align-items: center
}

.example-gird-demo .Media--reverse>.Media-figure {
	margin: 0 0 0 1em;
	-webkit-box-ordinal-group: 2;
	-ms-flex-order: 1;
	order: 1
}

.example-gird-demo .Notice {
	margin-bottom: 1.5em;
	padding: 1.2em 1.5em;
	background-color: #edffdb;
	color: rgba(0,0,0,.6);
	font-size: .9em
}

.example-gird-demo .Section {
	padding: 0 1.5em
}

.example-gird-demo .Section:nth-child(2n) {
	overflow: hidden;
	background-color: hsla(31,15%,50%,.1)
}

.example-gird-demo .Section:after,.example-gird-demo .Section:before {
	display: block;
	visibility: hidden;
	height: 0;
	content: '\00a0'
}

.example-gird-demo .Section:before {
	margin-bottom: 1.5em
}

.example-gird-demo .Section:after {
	margin-top: 1.5em
}

@media (min-width:768px) {
	.example-gird-demo .Section {
		padding: 0 2em
	}

	.example-gird-demo .Section:before {
		margin-bottom: 2em
	}

	.example-gird-demo .Section:after {
		margin-top: 2em
	}
}

.example-gird-demo .Section-heading {
	text-align: center
}

@media (min-width:768px) {
	.example-gird-demo .Section-list {
		margin: 0 4em 2em;
		padding: 0
	}
}

.example-gird-demo .Site {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex;
	height: 100%;
	-webkit-box-orient: vertical;
	-webkit-box-direction: normal;
	-ms-flex-direction: column;
	flex-direction: column
}

.example-gird-demo .Site-footer,.example-gird-demo .Site-header {
	-webkit-box-flex: 0;
	-ms-flex: none;
	flex: none
}

.example-gird-demo .Site-content {
	padding: 1.5em 1.5em 0;
	width: 100%;
	-webkit-box-flex: 1;
	-ms-flex: 1 0 auto;
	flex: 1 0 auto
}

.example-gird-demo .Site-content:after {
	display: block;
	visibility: hidden;
	margin-top: 1.5em;
	height: 0;
	content: '\00a0'
}

@media (min-width:768px) {
	.example-gird-demo .Site-content {
		padding-top: 2em
	}

	.example-gird-demo .Site-content:after {
		margin-top: 2em
	}
}

.example-gird-demo .Site-content--full {
	padding: 0
}

.example-gird-demo .Site-content--full:after {
	content: none
}

.example-gird-demo .u-ieMinHeightBugFix {
	display: -webkit-box;
	display: -ms-flexbox;
	display: flex;
	-webkit-box-orient: vertical;
	-webkit-box-direction: normal;
	-ms-flex-direction: column;
	flex-direction: column
}

.example-gird-demo .u-full {
	width: 100%!important
}

.example-gird-demo .u-1of2,.example-gird-demo .u-full {
	-webkit-box-flex: 0!important;
	-ms-flex: none!important;
	flex: none!important
}

.example-gird-demo .u-1of2 {
	width: 50%!important
}

.example-gird-demo .u-1of3 {
	width: 33.3333%!important
}

.example-gird-demo .u-1of3,.example-gird-demo .u-2of3 {
	-webkit-box-flex: 0!important;
	-ms-flex: none!important;
	flex: none!important
}

.example-gird-demo .u-2of3 {
	width: 66.6667%!important
}

.example-gird-demo .u-1of4 {
	width: 25%!important
}

.example-gird-demo .u-1of4,.example-gird-demo .u-3of4 {
	-webkit-box-flex: 0!important;
	-ms-flex: none!important;
	flex: none!important
}

.example-gird-demo .u-3of4 {
	width: 75%!important
}

@media (min-width:384px) {
	.example-gird-demo .u-small-full {
		width: 100%!important
	}

	.example-gird-demo .u-small-1of2,.example-gird-demo .u-small-full {
		-webkit-box-flex: 0!important;
		-ms-flex: none!important;
		flex: none!important
	}

	.example-gird-demo .u-small-1of2 {
		width: 50%!important
	}

	.example-gird-demo .u-small-1of3 {
		width: 33.3333%!important
	}

	.example-gird-demo .u-small-1of3,.example-gird-demo .u-small-2of3 {
		-webkit-box-flex: 0!important;
		-ms-flex: none!important;
		flex: none!important
	}

	.example-gird-demo .u-small-2of3 {
		width: 66.6667%!important
	}

	.example-gird-demo .u-small-1of4 {
		width: 25%!important
	}

	.example-gird-demo .u-small-1of4,.example-gird-demo .u-small-3of4 {
		-webkit-box-flex: 0!important;
		-ms-flex: none!important;
		flex: none!important
	}

	.example-gird-demo .u-small-3of4 {
		width: 75%!important
	}
}

@media (min-width:576px) {
	.example-gird-demo .u-med-full {
		width: 100%!important
	}

	.example-gird-demo .u-med-1of2,.example-gird-demo .u-med-full {
		-webkit-box-flex: 0!important;
		-ms-flex: none!important;
		flex: none!important
	}

	.example-gird-demo .u-med-1of2 {
		width: 50%!important
	}

	.example-gird-demo .u-med-1of3 {
		width: 33.3333%!important
	}

	.example-gird-demo .u-med-1of3,.example-gird-demo .u-med-2of3 {
		-webkit-box-flex: 0!important;
		-ms-flex: none!important;
		flex: none!important
	}

	.example-gird-demo .u-med-2of3 {
		width: 66.6667%!important
	}

	.example-gird-demo .u-med-1of4 {
		width: 25%!important
	}

	.example-gird-demo .u-med-1of4,.example-gird-demo .u-med-3of4 {
		-webkit-box-flex: 0!important;
		-ms-flex: none!important;
		flex: none!important
	}

	.example-gird-demo .u-med-3of4 {
		width: 75%!important
	}
}

@media (min-width:768px) {
	.example-gird-demo .u-large-full {
		width: 100%!important
	}

	.example-gird-demo .u-large-1of2,.example-gird-demo .u-large-full {
		-webkit-box-flex: 0!important;
		-ms-flex: none!important;
		flex: none!important
	}

	.example-gird-demo .u-large-1of2 {
		width: 50%!important
	}

	.example-gird-demo .u-large-1of3 {
		width: 33.3333%!important
	}

	.example-gird-demo .u-large-1of3,.example-gird-demo .u-large-2of3 {
		-webkit-box-flex: 0!important;
		-ms-flex: none!important;
		flex: none!important
	}

	.example-gird-demo .u-large-2of3 {
		width: 66.6667%!important
	}

	.example-gird-demo .u-large-1of4 {
		width: 25%!important
	}

	.example-gird-demo .u-large-1of4,.example-gird-demo .u-large-3of4 {
		-webkit-box-flex: 0!important;
		-ms-flex: none!important;
		flex: none!important
	}

	.example-gird-demo .u-large-3of4 {
		width: 75%!important
	}
}

.example-gird-demo .u-smaller {
	font-size: .85em
}

.example-gird-demo .u-bigger {
	font-size: 1.2em
}

.example-gird-demo .icon-big {
	font-size: 1.5em
}

.example-gird-demo .hljs {
	display: block;
	overflow-x: auto;
	padding: .5em;
	background: #f8f8f8;
	color: #333;
	-webkit-text-size-adjust: none
}

.example-gird-demo .diff .hljs-header,.example-gird-demo .hljs-comment {
	color: #998;
	font-style: italic
}

.example-gird-demo .css .rule .hljs-keyword,.example-gird-demo .hljs-keyword,.example-gird-demo .hljs-request,.example-gird-demo .hljs-status,.example-gird-demo .hljs-subst,.example-gird-demo .hljs-winutils,.example-gird-demo .nginx .hljs-title {
	color: #333;
	font-weight: 700
}

.example-gird-demo .hljs-hexcolor,.example-gird-demo .hljs-number,.example-gird-demo .ruby .hljs-constant {
	color: teal
}

.example-gird-demo .hljs-doctag,.example-gird-demo .hljs-string,.example-gird-demo .hljs-tag .hljs-value,.example-gird-demo .tex .hljs-formula {
	color: #d14
}

.example-gird-demo .hljs-id,.example-gird-demo .hljs-title,.example-gird-demo .scss .hljs-preprocessor {
	color: #900;
	font-weight: 700
}

.example-gird-demo .hljs-list .hljs-keyword,.example-gird-demo .hljs-subst {
	font-weight: 400
}

.example-gird-demo .hljs-class .hljs-title,.example-gird-demo .hljs-type,.example-gird-demo .tex .hljs-command,.example-gird-demo .vhdl .hljs-literal {
	color: #458;
	font-weight: 700
}

.example-gird-demo .django .hljs-tag .hljs-keyword,.example-gird-demo .hljs-rule .hljs-property,.example-gird-demo .hljs-tag,.example-gird-demo .hljs-tag .hljs-title {
	color: navy;
	font-weight: 400
}

.example-gird-demo .hljs-attribute,.example-gird-demo .hljs-name,.example-gird-demo .hljs-variable,.example-gird-demo .lisp .hljs-body {
	color: teal
}

.example-gird-demo .hljs-regexp {
	color: #009926
}

.example-gird-demo .clojure .hljs-keyword,.example-gird-demo .hljs-prompt,.example-gird-demo .hljs-symbol,.example-gird-demo .lisp .hljs-keyword,.example-gird-demo .ruby .hljs-symbol .hljs-string,.example-gird-demo .scheme .hljs-keyword,.example-gird-demo .tex .hljs-special {
	color: #990073
}

.example-gird-demo .hljs-built_in {
	color: #0086b3
}

.example-gird-demo .hljs-cdata,.example-gird-demo .hljs-doctype,.example-gird-demo .hljs-pi,.example-gird-demo .hljs-pragma,.example-gird-demo .hljs-preprocessor,.example-gird-demo .hljs-shebang {
	color: #999;
	font-weight: 700
}

.example-gird-demo .hljs-deletion {
	background: #fdd
}

.example-gird-demo .hljs-addition {
	background: #dfd
}

.example-gird-demo .diff .hljs-change {
	background: #0086b3
}

.example-gird-demo .hljs-chunk {
	color: #aaa
}

.example-gird-demo pre {
	margin-bottom: 1.76471em;
	padding: 1.25em 1.5em;
	border-radius: 3px;
	background: hsla(31,15%,50%,.1);
	font-size: .85em
}

.example-gird-demo .twitter-follow-button {
	width: 230px!important
}

.example-gird-demo .twitter-color {
	color: #00aced
}
</style>
