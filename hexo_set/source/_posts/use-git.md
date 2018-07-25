---
title: Git—最先进的分布式版本控制系统
date: 2018-07-17 20:19:43
tags: [git,github,基础]
categories: [git,github]
---

<font size="5"><center>Git的一些常用操作命令</center></font>

<!--more-->

## 开始使用 **Git** ——*找到三颗痣*

### 创建版本库

首先到你需要建立版本库的目录，然后在命令行里输入：
```
$ git init
```

**ps：**该目录里会多一个**.git**目录，请不要随便修改里面的文件

### 添加文件

```
$ git add <file>
```

可以多次添加文件，然后一次性提交。

### 提交文件

```
$ git commit -m <message>
```

**-m** 后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

## **Git** 控制——*月关宝盒*

### 掌握状态和查看修改

```
$ git status
```

**git status** 命令可以让我们时刻掌握仓库当前的状态。

```
$ git diff <file>
```

在你修改文件但还没添加或提交的时候，**diff** 可以告诉你到底哪里修改了

```
$ git diff
```

### 版本回退

一旦你把文件改乱了，或者误删了文件，可以从最近的一个commit恢复，然后继续工作，而不是把几个月的工作成果全部丢失。

在实际工作中，我们脑子里怎么可能记得一个几千行的文件每次都改了什么内容，不然要版本控制系统干什么。版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用git log命令查看：

```
$ git log
```

其中打印出来的**commit** 那一行就是 这次版本的 **ID** 号， **ID** 号特别有用。

如果有一个文件叫 **use_git.txt** 最经有三次版本提交，比如commit1、commit2、commit3，**git log** 命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是**commit3**，上一次是**commit2**，最早的一次是**commit1**。
 
好了，现在我们启动月关宝盒，准备把**use_git.txt**回退到上一个版本，也就是**commit2**的那个版本，怎么做呢？

首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交**commit3**，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

现在，我们要把当前版本**commit3**回退到上一个版本commit2，就可以使用git reset命令：

```
$ git reset --hard HEAD^
```

好了，现在我们已经会退到版本**commit2** 了，最新的那个版本**commit3**已经看不到了！好比你从21世纪坐时光穿梭机来到了19世纪，想再回去已经回不去了，肿么办？

办法其实还是有的，只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个 **commit3** 的 **commit id** 是 **xxx**... ，于是就可以指定回到未来的某个版本：

```
$ git reset --hard xxx
```

好了，现在我们又退到版本**commit3** 了
版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为 Git可能会找到多个版本号，就无法确定是哪一个了。

现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的commit id怎么办？

在Git中，总是有后悔药可以吃的。当你用
```
$ git reset --hard HEAD^
```
回退到**commit2**版本时，再想恢复到**commit3**，就必须找到**commit3**的 commit id。Git提供了一个命令**git reflog**用来记录你的每一次命令：

```
$ git reflog
```

### 修改文件

场景一：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令：

```
$ git checkout -- file
```

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令
```
$ git reset HEAD <file>
```
就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考**版本回退**那一步，不过前提是没有推送到远程库。

### 删除文件

```
$ git rm <file>
```
然后：

```
$ git commit -m <message>
```

就把该文件从版本库里删除了。

如果误删了，可以：

```
$ git checkout -- <file>
```

**git checkout** 其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

## 远程仓库——*五百年前后的记忆*

### 添加远程仓库

现在的情景是，你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作，真是一举多得。

你首先在Github上新建一个仓库，如命名learngit，目前，在GitHub上的这个learngit仓库还是空的，GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。
现在，我们根据GitHub的提示，在本地的learngit仓库下运行命令:

```
$ git remote add origin git@github.com:<name>/learngit.git
```

添加后，远程库的名字就是**origin**，这是Git默认的叫法，也可以改成别的，但是**origin**这个名字一看就知道是远程库。

下一步，就可以把本地库的所有内容推送到远程库上：

```
$ git push -u origin master
```

把本地库的内容推送到远程，用 **git push** 命令，实际上是把当前分支master推送到远程。

由于远程库是空的，我们第一次推送master分支时，加上了**-u**参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：

```
$ git push origin master
```

把本地master分支的最新修改推送至GitHub，现在，你就拥有了真正的分布式版本库。

### 从远程库克隆

```
$ git clone git@github.com:<name>/<repo name>.git
```

也可以通过：

```
$ git clone https://github.com/<naem>/<repo name>.git
```

进行克隆。

## 分支管理——*一根毫毛，千万分身*

### 查看分支

```
$ git branch
```

### 创建分支

```
$ git branch <name>
```

### 切换分支

```
$ git checkout <name>
```

### 创建+切换分支

```
$ git checkout -b <name>
```

### 合并某分支到当前分支

```
$ git merge <name>
```

### 删除分支

删除合并过的分支：

```
$ git branch -d <name>
```

删除未合并过的分支：

```
$ git branch -D <name>
```

### 存储当前工作现场

```
$ git stash
```

查看存储列表

```
$ git stash list
```

恢复工作现场工作，有两个办法：

一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；

```
git stash apply <stash id（通过stash list查看）>
```

另一种方式是用git stash pop，恢复的同时把stash内容也删了：

```
$ git stash pop
```


参考文献：
[廖雪峰老师的Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)