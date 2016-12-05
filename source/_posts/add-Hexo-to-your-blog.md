---
title: 引入Hexo，美化你的博客（一）
tags: [hexo,blog,博客]
categories: [Blog]
date: 2016-12-04 00:25:46
---



为什么要使用[Hexo](https://hexo.io)
---
工具的使用，首先源于模仿，这是我对自己的定义。跟着别人的教程搭建这个博客的时候，我首先接触的静态网页框架是[Jekyll](https://github.com/jekyll/jekyll)。孰优孰劣，不由我说，还请君尝试。
提到为什么要使用`Hexo`,纯粹是因为`Hexo`这样类似的静态网页框架，有丰富的第三方修改的皮肤，用以美化自己的博客。

{% blockquote Hexo.io https://hexo.io/zh-cn/docs/index.html 什么是Hexo %}
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 [Markdown](http://daringfireball.net/projects/markdown/)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
{% endblockquote %}

为你的博客引入Hexo
---

### 前提
本文基于我对于`Github Pages`的浅薄认识，以及我对于博客系统搭建的三分钟热情，在我的博客搭建完毕之后，写下这篇文章用以记录过程并分享给大家，其间必定有写的不妥或者错误的地方。意见和建议请下方留言，不喜欢请悄悄的离开。

<!-- more -->

本文是从零开始搭建自己的博客，如果你的博客已经有了生产内容，请注意保护自己的资料，因为接下来将会清空所有的资料。

### 现在开始

#### 配置环境
本文将同时进行`MAC`和`Windows`系统下的安装过程进行说明。首先请确保自己的电脑具备

* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com/)

如果两项软件都已经安装就位，接下来将使用`npm`进行Hexo的安装。在Mac环境下直接进入终端，在Windows环境下，则推荐使用`git -bash`命令行终端进行操作。
```
$ npm install -g hexo-cli
```
待运行完毕之后，Hexo的基础环境就搭建好了。
#### 搭建博客
在电脑上合适的位置新建一个文件夹
```
$ mkdir Blog && cd Blog
```
当然，也可以手工创建一个文件夹
进入`Blog`之后，初始化你的第一个Hexo工程
```
$ hexo init
```
查询文档发现，可以直接通过`init`初始化到一个文件夹
```
$ hexo init [destination]
$ cd destination
```
初始化完毕
![hexo init finished](/images/hexo_init/hexo_init.png)
官方接下来一步使用了
```
$ npm install
```
进行模块的安装，不过网友指出在`init`阶段已经执行了模块安装工作。我在我的Mac上进行了验证，不执行`install`是可以正常运行的。
使用
```
$ hexo server
$ hexo s
```
两者之一就能够在本地打开一个Hexo server预览当前的blog。
![hexo s](/images/hexo_init/hexo_s.png)

一切正常的情况下，你的第一个博客就建立完毕了
![hexo blog](/images/hexo_init/first_blog.png)

### Q&A

**Q: 访问`localhost:4000`失败**
> **Answer**<br>目前网友们遇到最多的情况是和福昕阅读器的端口冲突，只需要修改端口就能够正常的运作。修改端口的方式有两种
> 第一种方式直接更换端口启动，该方式仅对当次有效
```
 $ hexo s -p 4001
```
>服务将在端口`4001`上启动
第二种方式直接修改物理启动端口，该方式永久有效
```
$ vim node_modules/hexo-server/index.js
```
>修改port到你喜欢的端口号即可
