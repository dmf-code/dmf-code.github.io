---
title: 解决github图床图片无法显示问题
date: 2022-01-25 21:39:29
tags: 问题集
categories: 问题集
cover: https://cdn.jsdelivr.net/gh/dmf-code/picture@main/picGo/20220125214108.png
---

#### 图片无法显示

最近发现 `github` 的图床被墙了，导致无法展示。为了解决这个问题需要将旧的图片链接全部替换为 `cdn` 的形式。

#### CDN

`jsdelivr` 作为国外的一家优秀的公共 CDN 服务提供商，我们可以借用它加速 Github 仓库文件的访问。

```text
https://cdn.jsdelivr.net/gh/githubusername/reponame@branchname/foldername/subfolder
```

其中：

- `gh` 表示来自 `GitHub` 的仓库
- `githubusername/reponame` 表示用户和仓库名
- `branchname` 是仓库的分支名
- `foldername/subfolder` 是仓库下面的文件夹名

#### picGo 工具

基本上使用 `github` 作为图床都离不开 `picGo` 这个图片工具，为了更方便的获取到 `cdn` 后的图片链接，我们可以
给它添加一个自定义的域名。

![](https://cdn.jsdelivr.net/gh/dmf-code/picture/picGo/20220125215751.png)

这里省略了仓库分支名，这样子我们就可以快乐的写文章了。
