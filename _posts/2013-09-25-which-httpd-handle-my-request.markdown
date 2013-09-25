---
layout: post
title:  "是哪个apache进程处理了我的请求"
date:   2013-09-23 11:27:33
category: apache
---

> 当一个HTTP请求过来了，如何查看是哪个HTTPD在处理这个请求呢？

---

首先用

> netstat -ntpea

查找监听80端口的进程并且状态为ESTABILISH的进程Inode

* -t TCP
* -p Show the PID and name of the program to which each socket belongs.
* -e Display additional information.  Use this option twice for maximum detail.
* -a Show both listening and non-listening sockets.

然后在PID列就可以找到进程号了。

如果出现无法显示PID的情况，可以给netstat 加-e参数打印进程的Inode值。

Linux中进程也是文件，所以肯定就有Inode，这时去/proc/*/fd/中查找这个这个值，查找到后获取

上级目录的进程号就可以了，查找命令如下：

> sudo ls -l /proc/*/fd/ | grep -E "{$Inode}" -A10 -B10


