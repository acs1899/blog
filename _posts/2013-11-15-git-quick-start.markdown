---
layout: post
title:  "git quick start"
category: linux
---

> 主要讲从安装到使用git，针对windows使用命令行模式（git虽然提供可视化操作界面但功能不全）

-------

### 安装

windows下安装参考群里的***github小项目共同开发超简易图文教程.pdf***里的安装部分

这里不多废话了。

安装好后，在cmd（命令行界面）里面敲一下git version看看是否安装成功，如果出现诸如command not found之类的说明安装不正确

### ssh 公钥

想要往服务器上提交代码，必须要本机有一份私钥，服务器上有一份公钥（配对的）。 要在你的本机生成一对公钥和私钥，然后将公钥交给
管理员，私钥不动，这样才可以提交代码。

#### 生成方法

在cmd中打 ssh-keygen，（windows下git安装好后应该会有这个命令），打完命令后按3次回车就OK。然后在
{% highlight ruby %}
#1.注意将username改成你自己机器的名字
#2. .ssh文件夹可能为隐藏文件夹，需要先设置（妈的windows无比蛋疼）
C:\Documents and Settings\username\\.ssh\ 
{% endhighlight %}
文件下，有rsa和rsa.pub文件，rsa为私钥，不用管，rsa.pub为公钥，将这个文件发送给管理员即可

***我没有实验过，所以在ssh-keygen的时候最好看一下输出的信息，应该会有钥匙生成的位置什么的***

### 使用git

#### 初始化及获取项目代码

一切配置好之后，就可以部署代码了

在命令行里执行以下操作

{% highlight ruby %}
#cd到你的项目目录下，如cd D:\wamp\www\snspay\
cd $YOUR-PROJECT-DIRECTORY
git init
git remote add origin git@23.228.209.58:/snspay.git
git pull origin master
{% endhighlight %}

不出意外的话，执行完上述操作，在本地就会自动有一份项目代码了。

#### 提交更改

开发完或修复完BUG之后，执行以下命令

{% highlight ruby %}
# 注意这里的命令也要在项目的根目录里执行
git add -A
git commit -m "这里随便写你这次干了哪些事情"
git push origin master
{% endhighlight %}

OK，代码已经提交到测试环境上了，用http://develope.snspay.com可以查看到你的变更.

***不要在项目里面放没有用的文件，因为在提交时会连这些没用的文件一起提交***

### 给前端的一些tips

前端可能需要不断的提交代码来看效果，明显用上述方法会显得很麻烦。

可以参考[这篇][tips]（还没写）文章来看如何在本地搭一个snspay的开发环境。

善用google chrome 的 F12工具，在chrome浏览器中按下F12，可以查看网页HTML代码，CSS文件，JS文件等。

并且可以手动修改这些CSS和HTML，修改效果会马上呈现在页面中，还可以直接执行javascript代码，环境与“当前环境”相同。

遇到问题先思考，思考不下来再谷歌，谷歌不下来去提问（社区最好）。

