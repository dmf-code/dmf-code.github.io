---
title: go_get代理问题
date: 2021-05-09 19:14:10
tags: go
categories: go问题集
cover: https://cdn.jsdelivr.net/gh/dmf-code/picture@main/picGo/20210509191456.png
---

### 问题

使用 `go get` 命令无法成功安装第三方库，这问题是被墙了导致的。

### 解决方案

添加国内镜像 `https://goproxy.cn/` 这个网站是七牛云支持的，七牛是最早做这方面的，所以是当前最佳的方案。

![image.png](https://cdn.jsdelivr.net/gh/dmf-code/picture@main/picGo/20210509191525.png)

### 坑点

一开始学习 `GO` 的时候在网上也找到过这个镜像方案，但是却一直出现 `404` 的情况。这里以一个测试库为例

```shell
go get -u github.com/stretchr/testify
```

然后出现如下的问题

```shell
go get github.com/stretchr/testify: module github.com/stretchr/testify:
Get "https://goproxy.cn/github.com/stretchr/testify/@v/list": Method Not Allowed
```

这个问题很奇怪，所以在我刚入门的时候一直配置也无法使用国内镜像。然后找到了一种另类的方法

```shell
set http_proxy=127.0.0.1:1080
set https_proxy=127.0.0.1:1080
```

这样子我们就可以通过自己搭建代理的形式来 `go get` 请求安装了，不过这种严重依赖翻墙，最近 `ssr` 都被封了，然后我使用的另类方法自然就无法使用。

在官方的 `issue` 中提问题才解决掉，可能是我之前有设置过代理的原因。才会导致一直 `404` 的情况出现，然后将代理置空后就可以了。

```shell
set http_proxy=
set https_proxy=
```

这次确实是被环境给坑了，也是自己对于问题的处理能力不足的体现。

> GoLand 编辑器需要使用 go list -m -json all 进行包加载

[goproxy issue](https://github.com/goproxy/goproxy.cn/issues/105)
