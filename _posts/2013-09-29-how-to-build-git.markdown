---
layout: post
title:  如何用github pages搭建一个博客
date:   2013-09-29 11:27:33
categories: github
---

> 本文讲述如何用github来搭建一个博客系统, 主要讲了下搭建流程。

### github pages

github pages本意是快速给项目或个人创建展示性的页面, 搭建在github的服务器上, 并支持独立域名; 但是上面只能放静态站点, 但这完全
不影响我们用他来作博客, 因为他支持强大的jekyll引擎.

### 利弊

#### 好处

1. 免费稳定的服务器能让我们有更多的钱去买吃的.
2. 很多现成的用github pages搭建的站点, 均是开源的, 如果喜欢某一套网站的模板, 可以悄悄地fork到其源码, 遵循一些协议就可以占为己用, 在[这里][jekyshowsites]可以看到很多用github pages + jekyll开发的网站.
3. 网站可以随意定制.
4. 既然是github的东西自然少不了版本控制.
5. 静态页面加载速度快.
6. 使用markdown语法, 直接用Vim写出统一风格的漂亮博文.
7. ...

#### 坏处

1. 静态页面, 部分交互功能实现起来比较麻烦.
2. markdown乱解析问题.
3. GFW有时要使坏.

### 在github pages上写一个hello kitty(不是hello world)

首先得去github上建立一个名为{username}.github.io的项目, 并在本地pull到项目, 写一个hello kitty到index.html, 第一次push上去后, 需要等一个大便的时间, 才可以用{username}.github.io来访问到页面. 域名解析在根目录中建立一个名为CNAME的文件, 文件里填写域名就可以了, 修改这个文件后, 要用域名访问博客, 也需要等上一个大便的时间.

好了, 既然现在页面已经能正常访问. 就可以放上任意的静态东西了.

### jekyll

现在可以直接通过手写html来搭建博客, 不过这工作量太大, 现在就是jekyll派上用场的时候了, 官方文档在[这里][jekyllofficial].

可以在本地配一个jekyll, 测试比较方便, 如果不想搭配本地环境, 可以直接将代码推到github上来浏览页面.

本地可以直接查看文档, 用

> sudo jekyll dosc

运行完毕后在浏览器输入localhost:4000就能看到跟官方网站一模一样的东西了.

#### 安装

> sudo apt-get install rubygems

> gem install jekyll

> jekyll new myblog

gem是ruby提供的包管理工具.
jekyll new 实现自动创建一些目录, 现在cd到myblog就可以看见jekyll自动帮我们生成的文件结构了.

windows用户可以参考[这篇文章][windowsjekyll].

### front-matter

每个文件在开始出可以有一些头部信息, 格式如下:

{% highlight ruby %}

---
#this is configure
---

{% endhighlight %}

这里可以制定该文件的一些属性 

**layout**: 指定该页面用哪个模板.

**title**: 文章的标题.

**category**: 文章分类.

****: 

#### 目录结构

所有特殊的目录都是以下划线开头.

* _config.yml

这个是文件, 里面放存放配置信息, 常用配置如下

**destination**: 这里配置jekyll将编译好的网站放到的地方, 默认放入build

**safe**: 是否允许插件.

**server_port**: jekyll服务的端口, 如果4000被占用了就修改这个端口号.

**permalink**: 自定义url模板, 参考[这篇][jekylllinks]文章.

**pygments**: true则自动设置代码高亮.

**markdown**: 设置markdown的编译器, 比较重要, markdown不同编译器的编译的结果可能有些不同, 并且有些对中文支持不够, 这里建议设置为 redcarpet.

**paginate**: 设置每一页显示的item数量, 配合paginate_path使用, 有关分页的说明参考[这里][jekyllpage].

常用大概就这么多了, 具体看自己的需求, 全部配置信息可以从[这里][jekyllconfig]获得.

* _layout

这是文件夹, _layout文件夹存放html文件.

layout中是放模板的地方, 每个页面可以制定其模板, 指定了以后, 页面就会自动被layout填充. 编译后的页面, 会将layout引进过来, 页面本身的内容写在layout文件中 **{{ content }}** 的地方.

* _include

这是文件夹, _include文件夹存放html文件.

include中存放一些常用的html文件, 如头部和脚步. 这个在大多的模板引擎中已经见惯了, 这里就不细谈了. 可以在任意文件中将 include 中的文件夹中的文件通过 **{% include footer.html %}** 引用进来.

* _posts

这是文件夹, _posts文件夹存放markdown文件.

posts中就是存放博文的地方了, 文件名格式是 

> YYYY-mm-dd-your-blog-title.markdown 

比如本篇文章对应的post文件名是

>2013-09-29-how-to-build-git.markdown

博文使用markdown语法书写, [这里][markdown]是markdown教程.

### 部署

jekyll的部署很简单, 在本地的话直接执行

> jekyll build

然后在build文件夹中, 可以看到生成的网站代码了. 然后用

> jekyll serve --detach

jekyll serve表示开启服务, 默认4000端口, --detach表示后台运行. 现在访问localhost:4000就可以看到博客了.

在github上部署, 直接将**项目**根目录(注意这里不需要将build目录加进去), push到{username}.github.io就可以了.

github会自动识别这是一个jekyll项目并进行parse

### 分类


[markdown]: http://wowubuntu.com/markdown/
[jekyllconfig]: http://jekyllrb.com/docs/configuration/
[jekyllpage]: http://jekyllrb.com/docs/pagination/
[jekylllinks]: http://jekyllrb.com/docs/permalinks/
[jekyshowsites]: https://github.com/mojombo/jekyll/wiki/Sites
[jekyllofficial]: http://jekyllrb.com/docs/home/ 
[windowsjekyll]: http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html
