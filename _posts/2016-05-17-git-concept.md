--- 
layout: post 
title: git 核心概念简析 
category: Tech 
tags: git 
---

* 提交对象
* 分支
* 工作区域和文件状态
* 分布式 Git简述



提交对象
-
![首次提交对象及其树结构](https://git-scm.com/book/en/v2/book/03-git-branching/images/commit-and-tree.png)
![提交对象及其父对象](https://git-scm.com/book/en/v2/book/03-git-branching/images/commits-and-parents.png)

1. commit提交对象：包含一个指向tree的指针和所有提交信息。
 - 非首次提交时还包含一个指向上次提交对象（父对象）的指针。
2. tree树对象：记录着目录结构和 blob 对象索引。
3. blob 对象：保存着文件快照。

分支
-
![HEAD 分支随着提交操作自动向前移动](https://git-scm.com/book/en/v2/book/03-git-branching/images/advance-testing.png)

1. HEAD指针：指向当前所在的本地分支的特殊指针
2. master分支：默认的初始分支
3. testing分支：新创建的分支

**图中有4个commit对象，默认的master分支和创建的testing分支。初始时HEAD指向master分支，然后checkout到testing分支。此时再次提交后testing分支自动向前移动，HEAD仍指向testing分支。**

- 注意：分支切换会改变你工作目录中的文件，记住HEAD表示本地工作目录。

工作区域和文件状态
-
![](https://git-scm.com/book/en/v2/book/01-introduction/images/areas.png)

1. 工作目录
2. 暂存区域
3. 文件仓库，git目录

![](https://git-scm.com/book/en/v2/book/02-git-basics/images/lifecycle.png)

1. 未追踪状态（其他都是已追踪）
2. 未修改状态
3. 已修改状态
4. 已添加到暂存区域，即暂存状态

**一个工作目录下的文件在这四种状态中必居其一，比较特殊的是文件在暂存后又进行了修改，即同时处于已修改和已暂存状态。**

分布式 Git简述
-
理解分支的概念后，下面的图片也就容易理解了。
![推送所有的改动回服务器后 Jessica 的历史](https://git-scm.com/book/en/v2/book/05-distributed-git/images/small-team-7.png)
可以看出黄色部分，从738ee开始是另外一个人的分支，但是已经提交到origin/master。绿色部分，从fbff5开始是自己本地本地的issue54分支。必须先切回本地master分支ledee，分别合并origin/master和issue54分支后，才可以提交。


理解了以上概念后，再熟悉具体的指令将会轻松许多。