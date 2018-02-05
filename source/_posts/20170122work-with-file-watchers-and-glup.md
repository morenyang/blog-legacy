---
title: 使用File Watchers和Gulp打造便捷的前端自动化流程
date: 2017-01-22
foreword: 使用Jetbrains的IDE中的FileWatchers 功能和Gulp 前端自动化工具配合，打造便捷的前端自动化流程
---

和Grunt一样，Gulp也是当下很流行的一款前端自动化工具。Gulp自带的文件监视功能本身其实已经十分强大，但是搭配JetBrains全家桶时，快捷度不如JB自带的FileWatchers功能，而且会一直占用Terminal窗口。而JB自带的FileWatcher在功能上也有一些局限，例如不能像Gulp那样一次性完成一套很牛逼的文件处理。

所以如果我们开一下脑洞，让JetBrains中的FileWatchers功能主动监视文件变动，然后调用Gulp完成文件处理，这样的自动化流程会是多么的和谐美好。

##安装和配置Gulp

安装Gulp十分便捷。我们使用npm来安装Gulp。

首先，初始化一个npm项目：

```bash
$ npm init
```
然后，将Gulp作为项目的开发依赖安装(可能需要`sudo`）：

```bash
$ npm install gulp --save-dev
```

与Grunt使用`json`配置自动化流程不同，Gulp使用`js`来配置。在项目根目录下新建一个`gulpfile.js`文件：

```JavaScript
var gulp = require('gulp');

gulp.task('default', function{
	console.log('Hello Gulp! ');
});
```

这时候我们如果在终端中输入`gulp`，系统就会自动执行默认任务，这时候就可以看到屏幕上打印出的`Hello Gulp`了。

#### 自定义Gulp任务

我们使用`gulp.task(task_name, function(){})`命令来注册一项Gulp任务，使用`gulp task_name`来执行一项任务。

这里我们以使用Gulp讲sass文件编译成css文件为例，首先我们要安装`gulp-sass`插件（可能需要`sudo`）：

```bash
$ npm install gulp-sass --save-dev
```

然后在配置文件中编写：

```JavaScript
var gulp = require('gulp'),
    sass = require('gulp-sass');
	

gulp.task('sass', function () {
  return gulp.src('./sass/**/*.scss')
    .pipe(sass().on('error', sass.logError))
    .pipe(gulp.dest('./css'));
});
```

这时候如果我们只需要在终端中输入`gulp sass`，Gulp就会自动将项目中的所有sass文件编译成css了。这里可以注意的是，如果只想要编译指定某个文件夹的某一级目录下的sass文件，在`gulp.src`中的参数应设置成`./sass/*.sass`，如果你想要递归地讲所有子目录中的文件也编译成sass，参数就应设置成`./sass/**/*.sass`。

如果要使用Gulp自带的文件监视器，可以再后面加上：

```JavaScript
gulp.task('sass:watch', function () {
  gulp.watch('./sass/**/*.scss', ['sass']);
});
```
这样你每次修改文件并保存的时候，系统就会自动帮你编译sass了。

#### Gulp插件仓库
如果你还想要更多的Gulp插件，可以到[http://gulpjs.com/plugins/](http://gulpjs.com/plugins/)中查找和下载。

### 配置File Watchers

File Watchers的配置就相对简单得多了。我们以自动监视所有sass文件并最终将app.sass编译成app.wxss为例。

我们先设置好Gulp的配置文件，这里我们要多使用一个`gulp-rename`的插件来帮助我们重命名文件。这里是配置文件：

```JavaScript
const gulp = require('gulp'),
    sass = require('gulp-sass'),
    rename = require('gulp-rename');

gulp.task('sass', function () {
    console.log('sass-watch');
    return gulp.src('./app.sass')
        .pipe(sass({outputStyle: 'compressed'}).on('error', sass.logError))
        .pipe(rename({extname: '.wxss'}))
        .pipe(gulp.dest('./'));

});
```

接着配置File Watcher。打开设置，找到FileWatchers选项卡，添加`sass`的监视器。在Program中，我们在选中入口文件`Gulp.js`，（Mac中默认存放在`/usr/local/bin`文件夹下，如果没有找到，先使用`npm install gulp -g`全局安装Gulp并记下安装位置）然后在Arguments中直接填入我们需要执行的任务，这里是`sass`，Output选项留空即可。保存设置，进入IDE中就可以看到Gulp为我们服务了。

#### 最后再多说几句
其实使用Grunt也可以搭配FileWatchers进行同样的操作，配制方法也没有改变，如果有兴趣可以去尝试一下。