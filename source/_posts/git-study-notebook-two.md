---
title: git学习笔记（二）—— git的简单操作
tags: [git,tools]
categories: [Tools]
date: 2016-12-09 14:00:45
---

这是本系列学习笔记的第二篇
[git 学习笔记（一）—— 初识 git](http://wluo.me/2016/12/08/git-study-notebook-one/)

前言
---

本篇文章将简要的介绍 Git 的一般操作，满足使用 Git 作为你的版本控制系统的入门级需求。本文将按工程的一般操作顺序进行讲述。

获取 Git 仓库
---
对于一个工程，一般来说，我们都是从0开始就要进行版本控制。需要我们在新建这个工程之初就要添加 git 支持。也有少部分在工程进行中添加 git 支持的需求。但是，不论你是从何种情景下添加版本控制，都不影响接下来将要进行的操作。
又或者，我们中途加入别人的工程，则需要克隆（clone）别人的仓库。

<!--more-->

### 现有目录初始化仓库

对于第一种情况，只需要在当前目录下使用
```git
$ git init
```
该命令会在当前文件夹下生成`.git`隐藏子目录，该目录包含你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 的骨干。这时候，仅仅做了一些初始化的操作，项目下所有的文件都还没有被跟踪。

### 克隆现有的仓库

在第二种情况下，直接获得已经存在的 Git 仓库的拷贝，需要使用 `clone` 命令。
```git
$ git clone https://github.com/libgit2/libgit2
```
这个命令会在当前目录下创建一个名为“libgit2”的目录，并在这个目录下初始化`.git`文件夹，从远程仓库拉取所有的数据放入`.git`文件夹下。当前目录下也将拷贝所有当前分支的文件供开发和使用。当然，在克隆远程仓库的时候，可以自定义本地仓库的名字：
```git
$ git clone https://github.com/libgit2/libgit2 mylibgit
```
这将执行与上一条命令相同的操作，在本地创建的仓库名称则会变成`mylibgit`

追踪你的文件
---
现在我们有了一个真实存在的 Git 仓库，可以对仓库下的所有文件进行修改，完成阶段性目标之后，提交这些更新到仓库中。

在工作目录下的每一个文件不外乎两种状态：
	* 已跟踪	已经纳入版本控制的文件。
	* 未跟踪	除了已跟踪的文件，都属于未跟踪文件。
所有已跟踪的文件可能处于未修改，已修改和放入暂存区三种状态。所有使用 Git 时文件的生命周期如下：

![图1. 文件的状态变化周期](/images/git/lifecycle.png)

### 检查当前文件状态

使用`git status`命令可以查看当前目录下各个文件分别处在什么状态，一般来说，文件会处在四个不同的状态：

#### 状态一 clean
当你克隆一个工程下来，整个文件系统会处在这个状态
```git
$ git status
On branch master
nothing to commit, working tree clean
```
#### 状态二 untracked
在工程中新建一个“README.md”文件，并编辑其中的内容，这时候再查看状态：
```git
$ echo 'title' > README.md
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	
        README.md
	
nothing added to commit but untracked files present (use "git add" to track)
```
#### 状态三 staged
新建一个文件之后，文件并不会立即被 Git 追踪，因为很多文件我们并不希望其被版本控制。因此，整个过程希望我们能够有所选择，使用`git add <file>`命令添加你希望的文件进入版本控制中，处于`staged`状态的版本信息，在每个阶段的修改中，只存在一个版本。
```git
$ git add README.md
$ git status
On branch master
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
  	
	new file:   README.md
	
```
注意到，Git 提示可以使用
```
$ git rm --cached <file>
```
将已经处于`staged`状态的文件回退到`untracked`状态。
#### 状态四 committed
使用`commit`命令，将已经暂存起来的更改提交到仓库中，所有的历史版本追溯都是以你`commit`为基准进行回溯的。区别于`staged`状态下的文件，这样的版本信息是永久的。
```git
$ git commit -m 'first commit'
[master (root-commit) 33e08ff] first commit
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
	
$ git status
On branch master
nothing to commit, working tree clean
```
我知道，细心的你已经发现，这个状态和‘状态一’根本就没有区别嘛。之所以分别列出来，是想重点的强调一点，基于版本控制的整个文件系统，每次`commit`都是一个独立的版本。在 Git 的历史版本中都是独立并且一视同仁的。
#### 状态五 not staged
再次修改“README.md”文件，查看状态
```git
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
  	
	modified:   README.md
	
no changes added to commit (use "git add" and/or "git commit -a")
```
`not staged`状态下，处于`staged`状态的对立面，表示进行了修改等操作，但是所有的操作没有被 Git 暂存起来，这个时候可以使用`add`命令将修改暂存，也可以跳过这一步，直接使用：
```git
$ git commit -a -m 'message'
```
将所有的修改跳过暂存状态，提交到 Git 仓库。

### 提交修改到仓库
在做协同开发的时候，一般来说会有一个远程仓库做大家的同步和备份仓库使用。在做好自己的工作之后，提交到本地仓库之后是不够的。需要将本地的修改同步到远程仓库，大家合并各自的部分，才能拼凑成一个完整的工程。
#### 合并远程仓库
直接提交本地修改到远程仓库，很大可能会由于远程仓库超越本地分支进度而被拒绝提交。需要先合并远程的修改之后，解决冲突才能成功提交。
```git
$ git pull
```
`pull`将远程仓库的所有更新文件获取下来，解决冲突。再将文件`add`进来，`commit`之后提交。
```git
$ git push origin master
```
