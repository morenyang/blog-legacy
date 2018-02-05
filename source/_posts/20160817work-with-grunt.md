---
title: Grunt前端工具的配置入门
date: 2016-08-17
foreword: Grunt是比较流行的前端自动化构建工具。可以帮助我们简化和减少前端的工作。我们通过几个简单的步骤就可以搭建起Grunt工具的自动化构建流程。

---


[Grunt](http://gruntjs.com/)是比较流行的前端自动化构建工具。可以帮助我们自动完成例如CSS压缩、编译Less、js风格检查等工作，大大简化和减少前端的工作。~~总之你只要知道这个是很牛逼的工具就行了！~~


## 安装Grunt
### 安装前

Grunt和Grunt插件的安装和管理都基于npm。因此在安装Grunt前你需要先安装[Node.js](https://nodejs.org/)，并保证它的版本在`0.8.0`以上。如果你已经装了nodejs，在安装前，最好先更新一下npm（可能需要`sudo`）：`npm update -g npm`。

*另注，国内网络链接npm容易出问题，可以用淘宝的[cnpm](https://npm.taobao.org/)镜像代替。安装cnpm:*

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
*cnpm的功能和操作和npm一致，只需要把`npm`换成`cnpm`即可*


### 安装grunt-cli

接着就可以安装grunt-cli了，在终端中输入以下命令（可能需要`sudo`）:

```bash
npm install -g grunt-cli
```
这样Grunt命令就会被安装到你系统的资源库中，下面就可以在项目中配置Grunt了。


## 在项目中安装配置Grunt
### 准备配置文件
不论你是要讲Grunt配置到新项目或是已有项目中，都必须要准备两个文件：`package.json`和`Gruntfile.js`。**注意大小写！**


我们需要把这两个文件放到项目的根目录中，像这样：
![](http://morensblog-static.tengtengtengteng.com/img/post/20160817-work-with-grunt1.png)


两个文件都可以再Grunt官网中找到：[package.json](http://gruntjs.com/getting-started#package.json) / [Gruntfile.js](http://gruntjs.com/getting-started#an-example-gruntfile) 。

### 安装Grunt和插件
#### `package.json`
稍微解释一下，`package.json`中包含的是npm包中各种所需模块以及项目的配置信息。通过完善`package.json`文件，我们可以让npm命令更好地为我们服务。

```json
{
  //项目名称和版本
  "name": "my-project-name",
  "version": "0.1.0",
  
  //项目所需要的插件和版本
  "devDependencies": {
    "grunt": "~0.4.5",
    "grunt-contrib-jshint": "~0.10.0",
    "grunt-contrib-nodeunit": "~0.4.1",
    "grunt-contrib-uglify": "~0.5.0"
  }
}
```

配置完`package.json`后，我们只需要打开终端，在项目文件夹中输入 `npm install` 即可安装Grunt和所需要的插件。

当然，你也可以在终端中的项目文件夹下输入命令 `npm install <module> --save-dev ` 来手动安装Grunt和插件。


例如下面这行命令就可以安装Grunt模块：

```bash
npm install grunt --save-dev
```

或者通过下面这行命令安装less编译器：

```bash
npm install grunt-contrib-less --save-dev
```

*另注，插件和使用方法可以在[Grunt插件](http://gruntjs.com/plugins)页面中找到。*

### 配置`Gruntfile.js`

Gruntfile.js中存放的是Grunt工作时需要的配置文件，~~通过这个文件告诉它要怎么帮助我们干活~~。

```js
module.exports = function(grunt) {

  // 这里是任务中插件的配置代码
  // Project configuration.
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });
  
  // 这里告诉他需要加载哪些插件
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // 这里告诉它我们定义了哪些任务和任务需要调用哪些插件
  // Default task(s).
  grunt.registerTask('default', ['uglify']);

};
```

插件的配置代码可以在插件的主页中找到。

**到此，Grunt的安装和配置就全部完成了，你需要做的只是在终端中的项目文件夹下敲入`grunt`命令即可。**

## 实例演示
#### ~~前面说的都是废话，如果看上面不懂直接从这里开始看就好~~

例如，我们现在需要配置一个压缩CSS的任务，我们只需要

1. 把相应的文件放在项目文件夹中
2. 安装所需的[cssminify](https://www.npmjs.com/package/grunt-contrib-cssmin)等插件
3. 配置Grunt任务
4. 执行Grunt任务

### 项目准备
如图，把项目文件和Grunt配置文件准备好，并通过npm安装好`grunt-cli`。

![](http://morensblog-static.tengtengtengteng.com/img/post/20160817-work-with-grunt2.png)

### 安装所需插件
#### 1. 配置`package.json`

```json
{
  //项目名称和版本
  "name": "project",
  "version": "0.1.0",
  
  //项目所需要的插件和版本
  "devDependencies": {
    "grunt": "~0.4.5",
    "grunt-contrib-jshint": "~0.10.0",
    "grunt-contrib-nodeunit": "~0.4.1",
    //下面这行就是安装告诉npm我们要安装1.0.1版的cssmin插件
    "grunt-contrib-cssmin": "~1.0.1"
  }
}
```

#### 2. 通过npm安装需要的插件

这里为了快速我使用cnpm来安装

```bash
sudo cnpm install
```

如果安装没有出现问题，你的项目文件夹中应该会出现`node_modules`文件夹，用来存放npm安装的插件

### 配置Grunt任务

配置`Gruntfile.js`，这里我直接设置了压缩所有css文件。

```js
module.exports = function (grunt) {
    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        //这里写cssmin的配置（插件主页找得到）
        cssmin: {
            target: {
                files: [{
                    expand: true,
                    //你需要压缩的css文件夹的路径
                    cwd: 'src/css',
                    src: ['*.css', '!*.min.css'],
                    //压缩后输出的文件夹路径
                    dest: 'dist/css',
                    //输出的文件名后缀
                    ext: '.min.css'
                }]
            }
        }
        //如果有别的插件就在上面加个逗号，然后把配置文件写这里，以此类推
    });
    grunt.loadNpmTasks('grunt-contrib-cssmin');
    //注册一个名字叫cssminify,执行cssmin的任务
    grunt.registerTask('cssminify', ['cssmin']);
};
```
**注意！任务名不要和插件名一样，就如不要以`cssmin`作为任务名**

这里如果你需要一次完成例如压缩js又压缩css之类的任务，就可以注册一个新的任务，像这样：

```js
grunt.registerTask('minify', [‘uglifyjs’,'cssmin']);
```

### 执行Grunt任务

特别简单，终端中在文件夹下敲入`grunt <任务名>`即可，例如：

```bash
grunt cssminify
```

如果没有问题，终端中会出现类似的提示：

```bash
$ grunt cssminify
Running "cssmin:target" (cssmin) task
>> 1 file created. 1 kB → 805 B

Done, without errors.
```

这时，我们就可以在输出文件夹中看到输出的压缩后的css文件了。
![](http://morensblog-static.tengtengtengteng.com/img/post/20160817-work-with-grunt3.png)

## 小结

其实Grunt的配置并不难，当然这里只是一个简单入门，还有很多强大的和深入的功能可以去发掘。除了[Grunt官网](http://gruntjs.com/)，这里还有一个[Grunt中文文档](http://www.gruntjs.net/)可以参考。