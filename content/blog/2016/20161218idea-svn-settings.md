---
title: 使用Jetbrains的IDE编写SVN中的项目
date: 2016-12-18
foreword: 使用Jetbrains的IDE编写Version Control中的项目，这里以SVN为例
---

### 先扯几句没用的
我们在开发项目的时候也经常会使用版本控制系统来管理我们的代码，例如著名的代码托管网站GitHub或者自己搭建的Git服务器或SVN服务器。

每种版本控制系统都有各自的优点和缺点，虽然我个人比较偏向于使用git，然而由于某些原因项目必须使用SVN来管理。Jetbrains开发的一系列IDE中都自带了VCS，可以直接从Version Control中打开、编辑和提交代码。因此我们就来配置一个SVN项目。

### 在IDE中配置VCS项目
这里以IDEA为例子，在欢迎界面的右边我们可以看到`Check out form Version Control`的选项，点开就可以看到几种VCS的类别，选择你项目所对应的VCS，然后会进入一个仓库管理面板。
![](http://morensblog-static.tengtengtengteng.com/img/post/20161218-idea-svn1.png)

点击那个小加号，输入你VCS的服务器地址，然后这个仓库就会出现在你的仓库列表中，选择你项目的仓库，点击右下角的Checkout，然后选择这个项目在本机存放的位置，并输入你的VCS的账号密码，点击然后一路确定，IDE就会自动帮你下载项目到本地并自动打开项目了。
![](http://morensblog-static.tengtengtengteng.com/img/post/20161218-idea-svn2.png)

### 在IDE中提交这更新项目
进入项目以后，如果想要更新、提交项目或文件，选择文件或文件夹，在右键 - subversion/Git 的菜单中，就可以选择提交、恢复、更新的选项了。
![](http://morensblog-static.tengtengtengteng.com/img/post/20161218-idea-svn3.png)
![](http://morensblog-static.tengtengtengteng.com/img/post/20161218-idea-svn4.png)

#### 在Windows中配置的注意事项
值得注意的是，如果你使用的是Windows系统，[你需要手动配置SVN工具的路径](https://blog.jetbrains.com/idea/2013/12/subversion-1-8-and-intellij-idea-13/)~~*我不知道为什么我在mac中不用配置直接就可以用了*~~。Jetbrains的官网技术文档中向你指向了Apache的Apache Subversion Binary Packages网站，选择你系统对应的SVN工具，下载即可。我在我的电脑中选择的是[SlikSVN](https://sliksvn.com/download/)。

安装完后，你需要在`Settings - Version Control - Subversion` 页面中的`General`卡中选择`Use command line Client`并且指定相应的SVN程序位置。
![](http://morensblog-static.tengtengtengteng.com/img/post/20161218-idea-svn5.png)

同理，如果你使用的是Git来管理项目的话，也要安装对应的Git命令行工具，在这里就不再过多赘述。