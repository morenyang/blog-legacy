---
title: JS绑定数据到HTML模板
date: 2016-10-12 13:27:13
foreword: 利用JS把JSON数据处理并绑定到HTML模板中的一个比较好用的方法
---

这段时间写网站的时候遇到了一些很烦的问题，就是要把从后台获取到的JSON数据渲染成HTML内容，有时候还需要再对数据进行一次请求的处理。然后在二次处理数据的时候我遇到了不少问题，于是请教了eWind大神。

*这篇文章的内容参考eWind大神的文章：[JS 绑定数据到 HTML 模板的简易模式](http://ewind.us/2016/js-render-html-template/)*<!-- more -->

---

我们在工作的时候，常常会遇到一份像这样的JSON数组：

```JSON
var data = {
	"userid": "123456", 
	"name": "Maybe Fang", 
	"sex": 1, 
	"weChat": "abc123", 
	"city": "519000" 
}
```

然后我们需要做的是把这些数据渲染成这样的HTML代码：

```HTML
<div class="user-info-card" id="uic_123456">
  <div class="user-info-name">Maybe Fang</div>
  <div class="user-info-sex">Male</div>
  <div class="user-info-weChat">abc123</div>
  <div class="user-info-city">Zhuhai</div>
</div>
```

通常我们可能会这么写：

```JavaScript
var printer = function(data){
  var content = '<div class="user-info-card" id="uic_' + data.userid + '">'+
    '<div class="user-info-name">' + data.name + '</div>'+
    '<div class="user-info-sex">' + sexPrinter(data.sex) + '</div>'+
    '<div class="user-info-weChat">' + data.weChat + '</div>'+
    '<div class="user-info-city">' + cityPrinter(data.city) + '</div>'+
    '</div>';
  $(".user-info-list").append(content);
};

var sexPrinter = function(index){
  var sexList = ['', 'Male', 'Female'];
  return (index == 1 || index == 2) ? sexList[index] : 'Undefined';
};

var cityPrinter = function(cityCode){
  //...
  return '...';
}
```

然而这种写法虽然可行，但是会遇到一些问题：
- 数据处理时如果遇到二次Ajax处理请求较难完成（如由
- JS 代码里掺杂 HTML 降低可读性，尤其是代码中经常需要将 class 的引号用另一种引号包裹。
- 和数据无关的 CSS 样式也需要在 JS 里添加，增加了维护负担。
- 性能问题，需要反复调用 jQuery 而较为低效（当然也可以在手工拼接 HTML 字符串后一次完成渲染，但代码非常丑陋）。

因此我们可以这么写：
首先我们在所需的网页中搞一个 `<script>` 标签来存放HTML代码的模板，像这样：

```HTML
<script id="template_uic" type="text/x-custom-template">
  <div class="user-info-card" id="uic_%userid%">
    <div class="user-info-name">%name%</div>
    <div class="user-info-sex">%sex%</div>
    <div class="user-info-weChat">%weChat%</div>
    <div class="user-info-city">%city%</div>
  </div>
</script>
```

然后我们在渲染HTML文本时，就可以取出模板中的内容，利用JS做正则替换即可：

```JavaScript
var template = document.getElementById("template_uic").innerHTML;
var content = template.replace(/%userid%/, data.userid)
              .replace(/%name%/, data.name)
              .replace(/%sex%/), sexPrinter(data.sex))
              .replace(/%...%/, data.info);

// insert
document.getElementsByClassName('user-info-list').append(content);
// other things
```

我们也可以自定义方法来处理内容，这时候如果我们想要使用Ajax来二次处理数据就变得十分方便。例如，我们通过Ajax方法来通过邮编获取城市名：

```JavaScript
var getCityName = function(htmlContent, cityCode, callBack){
  var url = '/queryCity?id=' + cityCode;
  $.getJSON(url, function(response){
    htmlContent = htmlContent.replace(/%city%/, response.cityName);
    callBack(htmlContent);
  });
}

var template = document.getElementById("template_uic").innerHTML;
var content = getCityName(template, data.city, function(content){
  // insert
  document.getElementsByClassName('user-info-list').append(content);
});
```

当然，我们如果开一下脑洞，还可以做一些更猥琐的事情。
例如，我们可以替换 className / id 甚至是 tagName，像这样:

```HTML
<div class="%className%"></div>

<%tagName%></%tagName%>
```

最后感谢一下eWind大神对我遇到的这个问题的耐心解答，希望这篇文章对你们有帮助。