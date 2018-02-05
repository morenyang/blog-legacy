---
title: 为WebStorm配置less自动编译环境
date: 2016-08-19
foreword: 利用WebStorm自带的File Watcher工具，可以帮助我们自动编译less。

---

## Less

[less](http://lesscss.org/)是一个比较流行的前端样式语言。相比[sass/scss](http://sass-lang.com/)，less的语法更为接近原声的css，又相对简单，比较适合初学者上手。因此，你可以平滑地由css迁移到less。当然less也有一些不足，例如可编程的功能不够，继承比较麻烦等。著名的Twitter Bootstrap就是用less作为底层语言编译的~~*，不过据说bootstrap 4.0开始已经换做sass作为底层语言了*~~。

如果想要学习less，可以参考：[Less中文网](http://lesscss.cn/)

### 安装less编译器

我们使用npm来安装less编译器。在命令行中输入以下命令即可(可能需要`sudo`)：

```bash
npm install -g less
```

安装完后，npm会告诉你lessc的安装位置，例如`/usr/local/bin/lessc`或`C:\Users\Username\Appdata\Roaming\npm\lessc`。**你最好先记下这个地址。**

#### 编译less *~~（这一段不重要）~~*

*如果你要通过命令行编译less为css的话，直接输入下面这行命令即可：*
```bash
lessc fileName.less fileName.css
```

*这里还有一个[clean-css插件](https://github.com/less/less-plugin-clean-css)，可以帮你直接编译出压缩后的css。你可以通过命令`npm install -g less-plugin-clean-css`来安装。使用这个插件，你只需要这样输入命令：*

```bash
lessc --clean-css fileName.less fileName.min.css
```

## WebStorm

[WebStorm](https://www.jetbrains.com/webstorm/)是一个比较牛×的Web前端IDE，特别是在JavaScript的编译中。WS提供了丰富的插件、定制化功能和文件支持。~~缺点嘛，就是太贵太吃资源。~~在这里我们利用WebStorm自带的**File Watcher**工具来实现less的自动编译。

首先，我们用WebStorm打开我们的项目文件夹，然后新建一个.less文件。

![](http://morensblog-static.tengtengtengteng.com/img/post/20160820webstorm-less1.png)

打开这个less文件，屏幕上方就会出现提示：
 > File watcher 'Less' is available for this file.
 
之类的话，然后我们点击右边的**Add Watcher**即可进入配置界面。

如果没有的话，我们直接在任务栏中选择
> WebStorm > Preference > Tools > File Watchers
>
> （Windows平台应该从 File > Settings 下进入）

然后点击下方或是右边的加号来添加less编译器。

一般情况下来说，WebStorm会自动检测你电脑中是否已经安装了lessc并获取它的安装位置，然后自动帮你配置。如果没有，参考下面的配置即可：

#### mac

![](http://morensblog-static.tengtengtengteng.com/img/post/20160820webstorm-less2.png)

#### windows

![](http://morensblog-static.tengtengtengteng.com/img/post/20160820webstorm-less3.png)

解释一下几个可能会更改的选项，

- File type： 你需要处理的文件类型
- Program： 需要调用的文件处理器（如lessc、node等）
- Arguments： 编译参数（这里直接是文件名）
- Output paths to refresh： 输出文件夹和文件名

**Arguments** 项目中可以编译入参数，例如：如果要直接编译压缩后的.css文件，则可以在Arguments中输入：`--clean-css $FileName$`。

**Output paths to refresh** 项目中，可以在文件名前填入`$FileParentDir$/`，指向当前文件的上层目录。

#### via NodeJS ~~这段不重要~~

*我们也可以通过在File Watcher中调用nodejs来编译less。*

*具体配置如下：*
![](http://morensblog-static.tengtengtengteng.com/img/post/20160820webstorm-less5.png)

*其中，Program中填入的是nodejs的安装位置，而Arguments中填入的是 **lessc的位置（一般在默认的npm下载目录 `node_modules/less/bin` 中) + 参数 ***

另外要说一下的是，WebStorm中的内容是自动保存的。当你输入或删除代码时，File Watcher也会实时帮助你编译。

保存设置后，就可以编辑你的less文件，并看到编译后的css文件了。

