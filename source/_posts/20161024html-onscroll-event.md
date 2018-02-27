---
title: 网页滚动事件的简单利用
date: 2016-10-24
foreword: 利用网页滚动条事件在网页中添加返回顶部的小标签
---

我们在写网页的时候常常会利用到滚动事件，例如当网页下滑到某个位置的时候开始播放视频，或者是给网页在下滑到某个位置的时候自动在右下角添加一个返回顶部的标签等。<!-- more -->

### 监听滚动事件
首先我们要先明白如何检测网页的滚动事件。这里有一段简单的代码可以帮助你判断和输出网页的滚动情况:

```JavaScript
window.onscroll = function (){
  console.log(window.scrollY);
}
```
打开一个可以上下滚动的网页，把上面的代码输入到**浏览器的控制台**中，滚动页面时即可看到当前屏幕滚动的高度。

*当然如果你习惯使用jQuery的话也可以这么写*
```JavaScript
$(window).scroll(function (){
  console.log($(this).scrollTop());

  // 以上输出代码也可以换成下面这句，效果一样
  console.log(document.body.scrollTop);
});
```

### 为网页添加返回顶部的标签
知道了如何监听滚动事件以后，我们就可以开始利用它了。这里我们就以利用滚动事件来实现返回顶部的标签

首先我们要在网页中加入一个div标签，里面放置“返回顶部”四个字，并为它设置样式：

```html
<!-- 样式 -->
<style type="text/css">
#scroll_top{
  position: fixed;
  bottom: 80px;
  right: 20px;
  display: none;
}
</style>

<!-- 标签 -->
<div id="scroll_top">
  <a href="#">返回顶部</a>
</div>
```

然后我们再利用JavaScript的滚动事件让页面在下滑到一定高度后显示返回顶部的标签：

```html
<script type="text/javascript">
window.onscroll = function() {
  var scroll_top = document.getElementById('scroll_top');
  if(window.scrollY > 300){
    scroll_top.style.display = "inline";
  } else {
    scroll_top.style.display = "none";
  }
}
</script>
```

完整的示例代码如下
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <style type="text/css">
    #scroll_top {
      position: fixed;
      bottom: 80px;
      right: 20px;
      display: none;
    }
  </style>
</head>
<body>
  <div id="placeHolder" style="height: 300vh"></div>
  <div id="scroll_top">
    <a href="#">返回顶部</a>
  </div>

  <script type="text/javascript">
    window.onscroll = function() {
      var scroll_top = document.getElementById('scroll_top');
      if(window.scrollY > 300){
        scroll_top.style.display = "inline";
      } else {
        scroll_top.style.display = "none";
      }
    }
  </script>
</body>
</html>
```
