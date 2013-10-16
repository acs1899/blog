---
layout: post
title:  zabbix与外部监控脚本整合方案
date:   2013-10-16 11:27:33
categories: linux
---

> 自写的外部监控脚本可能需要单独处理报警触发条件，结果展现等问题。而触发条件可能遇到很复杂的情况，zabbix对于这类问题是一个很不错的方案，而且zabbix完全支持外部数据处理。本文介绍一个将zabbix和外部监控脚本整合的方案。

---------

### 场景例子

UrlWatch是一个python脚本，监控部分url的状态，包括：

* code 返回代码
* span 执行时间

现在的需求是用zabbix来搜集code和span数据，然后决策是否发出报警，画统计图。

### 实现

#### 整体思路

外部脚本在一个 ***source*** 文件上打日志，日志格式是：

> service  item[params]=value  item[params]=value ...

service是服务，item是监控项，params是额外的参数，value是对应值

对应到UrlWatch的例子：

{% highlight python %}
urlwatch  code[www.baidu.com]=200   span[www.baidu.com]=0.001
urlwatch  code[www.sina.com]=302    span[www.sina.com]=0.00001
{% endhighlight %}

然后一个pnp(pull n push)守护程序去监控这个 ***source*** 文件的新添加的内容（类似tail -F），
一旦检测到新内容后，读取并进行PARSE，然后与一个 ***discovery*** 文件内容做对比，
***discovery*** 文件保存的是discovery需要使用的JSON格式数据（见后文），如果service和item组合
不存在于discovery文件中，则以固定格式写进discovery文件。 之后调用zabbix\_sender将结果发送给
server端，实现zabbix搜集数据。
server端的discovery跑一个自定义的key（后文），key对应的程序拉取discovery文件的JSON数据稍微组合后返回给zabbix，实现基于source中的
service和item自动创建zabbix的item、trigger、graph等。
实现结构大致如下图：
![arch](/images/post/zabbix-workds-with-external-script/4.png)


#### discovery

在zabbix前端建立名为 ***external*** 的host，该host管理外部数据。
在host建立一个discovery。
![discovery](/images/post/zabbix-workds-with-external-script/1.jpg)

这里说明一下key，discovery支持自定义的发现，也就是可以用UserParameter来设定discovery的自定义key，自定义key返回固定格式的json数据，如下：
![discovery json](/images/post/zabbix-workds-with-external-script/2.jpg)
当data中每多一项，则discovery就认为多发现了一项。

还要注意这里key中还指定了参数，这两个参数是过滤data数组用的，也就是说从一个json数据中赛选出部分数据返回，不返回全部. Discovery本身也支持过滤，filter，可以指定macro来只返回我们关心的数据，不过经过测试只能支持单一的macro匹配。

在上面这个例子，我们可以通过FSNAME来过滤内容（只返回FSNAME字段的内容匹配的项），但不能通过FSNAME 和 FSTYPE来过滤（实验结果，官方文档没做介绍），所以这里改用参数来进行过滤。由于返回数据是由自定义key来做，所以我们可以随意得根据参数来过滤内容（这部分不熟悉的可以参考zabbix UserParameter）。

然后我们需要做的就是自写一个脚本作为external.discovery的执行脚本，并支持传入参数。返回的数据格式符合标准就OK。

#### item prototype

接下来的工作就是创建item prototype，一旦discovery发现新项，就会自动根据item样板创建实际的item，如果一个discovery下有多个item prototype，那么每发现一个新项（data数组中多一个元素），discovery就会创建多个item。trigger类似。
![item prototype](/images/post/zabbix-workds-with-external-script/3.jpg)
呃，这里注意macro的使用就行了，我把所有的外部脚本的key都明明为external，从参数去区分他们。
item设置里可以使用descovery返回数据中的macro（就是data数组中每个元素的键名，注意这里宏的符号是#），type必须选择Zabbix trapper，因为要用到zabbix sender主动推送数据过来。

zabbix前端暂时就这样。

#### script

点[这里][github]查看代码

[github]: https://github.com/louis-sherren/l2zabbix
