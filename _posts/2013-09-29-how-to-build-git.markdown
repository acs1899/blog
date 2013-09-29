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


