---
title: Laravel结合tars使用感想
date: 2021-05-09 18:32:51
tags: tars
categories: tars
cover: https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509185102.png
---

### 序

使用 [laravel-tars](https://github.com/luoxiaojun1992/laravel-tars) 这个扩展包做开发已经一年了，越来越觉得 `laravel` 结合 `tars` 使用非常的蹩脚。我刚接触 `tars` 的时候，刚好官方也进行
推广使用。但是好景不长，官方文档虽然是越来越完善了，但是论坛简直是一潭死水，没啥人使用 `tars` 进行开发。

### 问题如何解决？

把 `tars` 当做硬骨头来啃下，要看 `tars` 的 `c++` 代码时不可能的了，毕竟这个工程量非常大，没必要将时间浪费在这里，我们通常可以相信框架的健壮性。然后就是 `tarsphp` 这一块必然就需要啃下，看这块
的时候，我发现之前负责这块的阅文集团好像也是将 `tarsphp` 全部转移到 `tarsjava` 了。导致里面很多问题提了 `issue` 解决很慢，不过这块不大，慢慢的我也了解整体的代码了。

### 提 issue 的路上

因为历史遗留的问题，我们公司使用的都是 `laravel-tars` 这个框架，这个是罗晓俊开源的一个项目，技术是非常牛逼的，不过因为用户量少，并且作者可能也没有投入到生产环境，在更新版本的时候会出现一些意想不到的 `bug`, 我也是多次给其提 `issue`, 也算是为 `tarsphp` 生态尽一份力吧。

### 维护成本

 `laravel-tars` 现在是单人维护，所以这个框架的版本停留在 `laravel 5.5` , 这个其实是一个非常致命的问题，因为现在 `5.5.*` 也是脱离了 `tls` 了。而且开源作者的重心也是不在上面了，要使用 `tars` 这套架构就要多留心了，成本是非常高的。

### 问题总结
1. `Carbon` 包需要不兼容升级去修复旧版本问题，然而 `laravel` 版本还是 `5.5` 导致 `Carbon` 无法升级
2. `laravel` 不支持协程
3. `laravel` 项目结构复杂与 `swoole` 结合后很容易出现内存泄漏问题







