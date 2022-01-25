---
title: docker-remote-api使用
date: 2021-08-12 23:17:08
tags: docker
categories: docker
cover: https://cdn.jsdelivr.net/gh/dmf-code/picture@main/picGo/20210812231825.png
---

### 为什么使用 docker remote api?

在使用 `docker` 的 `sdk` 进行开发时，我是在本地进行开发的，然后 `docker` 环境在虚拟机中，然后导致我现在写好代码进行测试都要进行代码上传，导致开发体验极差，所以没有使用 `docker remote api` 解决这个痛点。（这里使用虚拟机本地文件映射也行，不过 `VMware` 映射有点烦琐，就没有使用这种方式了）

### 流程

1. 查看是否打开了 `docker` 服务

```shell
systemctl status docker
```

2. 如果开启服务了需要关闭服务，不然 `docker` 会检测到重复启动。

```shell
systemctl stop docker
```

3.开始 `remote api`

```shell
dockerd -H=0.0.0.0:2375 -H unix:///var/run/docker.sock
```

### 实践

我这里使用的是 `go` 的 `docker/client` 包，所以要在环境变量中配置 `DOCKER_HOST`

```env
DOCKER_HOST=http://192.168.3.121:4443
```

具体的 docker go sdk 可以在 [Develop with Docker Engine SDKs | Docker Documentation](https://docs.docker.com/engine/api/sdk/) 找到。

![](https://cdn.jsdelivr.net/gh/dmf-code/picture@main/picGo/20210813220533.png)

请求返回 version 返回 docker 版本说明就是配置成功了。
