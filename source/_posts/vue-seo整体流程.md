---
title: vue-seo整体流程
date: 2021-04-07 19:18:16
tags: seo
categories: 捣鼓小东西
---

### 序

为了增加博客的流量，所以做一个 `seo` 优化。


### 工作流程

1. 使用 `go` 搭建一个 `http` 服务，并具备缓存功能。（浏览器执行最少7s时间）
2. 部署 `python` 编写的一个 `automationFramework` 项目，用来执行具体的请求渲染
3. `nginx` 对爬虫头信息识别，并对其反向代理到 `go` 的 `http` 服务中
