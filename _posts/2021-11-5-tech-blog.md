---
layout: post
title: "git 的 merge 以及 revert 操作"
subtitle: '使用 git 进行版本合并和版本回退'
author: "Tang Jibin"
header-img: img/header/2021-11-5-tech-blog.png #不需要加图片就用 header-img: text
#header-img-credit: CG #在图片右下角加 credit
header-mask: 0.5 #表示图片的阴影指数，只在标题加图片才需要
hidden: false
mathjax: false
mermaid: false
tags:
  - git
---

## 背景

对于版本合并，最近在做学校编译原理实验的时候，遇到了一个问题。在我 fork 了实验项目之后，原实验项目又有了新的 push，我想要我的仓库更新，但是我已经做了一部分实验了，所以肯定不能简单地 delete 后再 fork

对于版本回退，就是单纯地想记录一下，防止自己忘记

## 版本合并 merge

### 设置 upstream

首先进入本地仓库打开终端，输入命令 `git remote -v` 查看远程仓库的路径

如果未设置 `upstream` 初始只会显示 `origin` ，就是你的远程仓库路径

![](/img/in-post/2021-11-5-tech-blog/1.png)

执行命令 `git remote add upstream https://gitee.com/IceyBlackTea/cminus_compiler-2021-fall.git` 把你 `fork` 的仓库设置为你的 `upstream` 。这个命令执行后，没有任何返回信息；所以再次执行命令 `git remote -v` 检查是否成功。

![](/img/in-post/2021-11-5-tech-blog/2.png)

### 抓取 upstream 仓库的更新

执行命令 `git fetch upstream` 抓取原仓库的更新：

![](/img/in-post/2021-11-5-tech-blog/3.png)

由于我之前已经抓取了，且后来原仓库没有更新，所以我这里没有输出，实际上是会有输出的

### 合并并 push 到远程仓库

如果仓库有不同分支，就先要切换到要合并的分支，输入 `git checkout master` 切换到 master 分支

![](/img/in-post/2021-11-5-tech-blog/4.png)

执行命令 `git merge upstream/master` 合并远程的 master 分支

![](/img/in-post/2021-11-5-tech-blog/5.png)

由于我之前已经更新了，所以我这里显示的是 `已经是最新的`

最后只要再 `git push` 就完成了

## 版本回退

### 记录版本号

首先输入命令 `git log` 可以得到每次提交的版本号

![](/img/in-post/2021-11-5-tech-blog/6.png)

commit 后面跟着的就是每次提交的版本号，HEAD 指向的就是本地仓库的当前版本，输入回车可以显示更多提交版本号，按 `q` 退出 `git log` ，记录下你想要回退到的版本的版本号

### 方法一 使用 git reset（不推荐）

输入命令 `git reset --hard 版本号` 来回退到你想要的版本（一般版本号输入前几个字符就可以了）

原理： `git reset` 的作用是修改 `HEAD` 的位置，即将 `HEAD` 指向的位置改变为之前存在的某个版本，如下图所示，假设我们要回退到版本一：

![](/img/in-post/2021-11-5-tech-blog/7.png)

这个时候本地仓库版本是落后于远程仓库的，所以 `git push` 的时候就会出现错误，只能输入 `git push -f` 进行强制 `push`

### 方法二 使用 git revert（推荐）

输入命令 `git revert 版本号` 来反做你想要的版本（一般版本号输入前几个字符就可以了。通常情况下，上面这条revert命令会让程序员修改注释，这时候程序员应该标注revert的原因，假设程序员就想使用默认的注释，可以在命令中加上-n或者--no-commit，应用这个参数会让revert 改动只限于程序员的本地仓库，而不自动进行commit，如果程序员想在revert之前进行更多的改动，或者想要revert多个commit。）

原理： git revert是用于“反做”某一个版本，以达到撤销该版本的修改的目的。比如，我们commit了三个版本（版本一、版本二、 版本三），突然发现版本二不行（如：有bug），想要撤销版本二，但又不想影响撤销版本三的提交，就可以用 git revert 命令来反做版本二，生成新的版本四，这个版本四里会保留版本三的东西，但撤销了版本二的东西。如下图所示：

![](/img/in-post/2021-11-5-tech-blog/8.png)

最后 `git push` 就可以了