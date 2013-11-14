---
layout: post
title:  "如何搭建一个自动部署的git server"
category: linux
---

> 该文介绍如何在centos下用gitosis搭建一个带自动部署功能的git server

-------

### 安装

* git 

{% highlight ruby %}
yum install git-core
{% endhighlight %}

* python setup tools

{% highlight ruby %}
yum install python-setuptools
{% endhighlight %}

* gitosis

{% highlight ruby %}

git clone https://github.com/res0nat0r/gitosis.git 
cd gitosis
python setup.py install  
 
{% endhighlight %}

### 配置

* 首先建立一个git用户，专门用来管理git仓库

{% highlight ruby %}
useradd -m git
{% endhighlight %}

* 准备一份公钥（ssh public key）

gitosis的初始化需要一份公钥，对应用户即为git repo的管理员。这里管理员对git repo的控制，实际上也是通过控制一个gitosis-admin.git来控制成员和其他repo的，admin-repo中有一份配置文档和一个keydir，keydir里面存放成员的公钥，后面会cover这一点。

现在处于方便，直接将本机用root账户作为repo的管理员，切到root下

{% highlight ruby %}
ssh-keygen
# 三次回车
ls ~/.ssh/id_rsa.pub
{% endhighlight %}

公钥生成了，即~/.ssh/id_rsa.pub

* 初始化gitosis

需要先切到git用户下再进行初始化

{% highlight ruby %}
su - git
gitosis-init < ~/.ssh/id_rsa.pub
sudo chmod 755 /home/git/repositories/gitosis-admin.git/hooks/post-update
{% endhighlight %}

这样在git的HOME文件夹中会出现repositories目录，即仓库目录，目录中有一个gitosis-admin.git目录，即上文提到的。
现在本机root账户拥有这个仓库的读写权限。

这里最后一行权限修改很重要，如果不修改则配置不会生效。

* 增加成员和仓库

用本机root身份将gitosis-admin.git pull下来

{% highlight ruby %}
git init
git remote add origin git@hostname_or_ipaddr:/gitosis-admin.git
# 注意这里只有一个 : 和一个 / 
git pull origin master
{% endhighlight %}

一切顺利的话管理仓库应该会被pull下来的
编辑gitosis.conf

{% highlight ruby %}
[group gitosis-admin]  #group代表一个组，gitosis-admin为组名
writable = gitosis-admin #writable的值为该组的成员可以写的仓库名
members = louis@laptop #members代表组员，多个组员用空格分割，组员名用username@host的格式
{% endhighlight %}

该配置的意思就是gitosis-admin这个组中有一个 louis@laptop成员，并且该组的成员对 gitosis-admin这个repo可写
因为这里组名和repo名一样，所以要注意区分，这两个是完全不同的

增加仓库

仓库不用手动创建，只要在配置文档中某成员对某个名称的仓库（该仓库可能还不存在！）可写，那么当该成员向这个远程仓库push的时候，gitosis会自动在/home/git/repositories下创建仓库对应的目录the_repo.git。

增加成员

如上文说的，增加成员直接在members中写出成员名字就行了，然后需要该成员把自己的公钥复制到keydir中，以 成员名.pub 的格式命名。

***以上这些配置必须要push之后才能生效！***

### 代码自动部署

这里用一个git的post-update HOOK就能完成

假设现在需要将web仓库提交来的代码自动部署到/var/www/html/目录下

首先需要将/var/www/html加入版本控制，并切将远程仓库设置为 web.git

{% highlight ruby %}
#hooks文件夹下存储了各种钩子的实例文件
#如果要添加post-update钩子，则将post-update.sample后面的.sample去掉即可，当用户提交后，git会自动在hooks目录下寻找名为post-update的文件，若找到则执行其中代码
cd /home/git/repositories/web.git/hooks
#创建post-update文件
touch post-update
vim post-update
{% endhighlight %}

在psot-update中加入以下代码

{% highlight ruby %}
DEPLOY_DIR=/var/www/html/
unset GIT_DIR #!important 如果不使用这句，是无法改变下面git pull的执行环境的
cd $DEPLOY_DIR
git pull origin master
{% endhighlight %}

这样当每次push之后，git都会自动在/var/www/html下自动执行一次git pull origin master，这样就实现了自动部署代码的功能。

***这里要注意所有文件目录的权限问题，要确保git用户拥有读写权限***
