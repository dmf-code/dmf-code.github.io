---
title: git小故事（持续更新）
date: 2021-05-22 00:34:16
tags: git
categories: git
cover: https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210522004017.png
---



### checkout 远端分支指定问题

#### 场景

使用 `git checkout -b dev-v2 origin/dev` 拉取新的本地分支，然后 `git pull` 操作

![](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210522011056.png)

#### 隐患

当两个习惯不一样的程序员进行协作的时候，有一定机会他帮你解决问题，来到你的环境 `git pull` 一下，神奇的事情发生了。你发现更新了不属于自己的代码下来了，然而你一直使用 `git pull origin dev-v2` 命令未曾遇到过，然后一头雾水，为什么会更新了别人的代码？

最后我们互相对望，相视而笑。（互相认为对方是煞笔 [狗头]）

#### 问题剖析

这里面其实就是远端分支在作怪，使用 `git checkout -b dev-v2 origin/dev` 命令会默认执行了 `git branch --set-upstream-to=origin/dev` , 然后你的远端分支就是 `origin/dev` 了。我们可以使用 `git branch -vv` 来查看相关远端分支。

![](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210522011131.png)

解决方法有两种：

1. 重新设置新的远端分支

```shell
git branch -u origin/dev-v2
git branch --set-upstream-to=origin/dev-v2
```

2. 先转到 `dev` 分支，然后 `git checkout -b dev-v2` 拉取本地分支

```shell
git branch -u origin/dev-v2
git branch --set-upstream-to=origin/dev-v2
```



两种方法其实也是要重新设置远端分支，但是比较推荐第二种。应为第一种默认就设置成 `origin/dev` 为远端分支，有时候会出现忘记重新设置远端分支的情况，然后使用 `git pull` 命令就会导致仓库代码被污染了。第二种情况就会提醒你需要设置远端分支，所以就能避免你粗心大意的问题。

#### 总结

出现这些问题其实也就是他们的 `git` 基础都不牢固，所以才没有立刻发现问题所在。最后想说的是 ***基础不牢，地动山摇***。







