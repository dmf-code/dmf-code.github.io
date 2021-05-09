---
title: 项目发布中的tars_start.sh是如何生成的？
date: 2021-05-09 18:54:17
tags: tars
categories: tars
cover: https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509185454.png
---

### 开始

发布任务之后，我们都会在 `bin` 目录下看见 `tars_start.sh` 文件。但是这个文件是如何获取的呢？为了探究这个问题，我们首先下载源码到本地。

```shell
git clone git@github.com:TarsCloud/TarsFramework.git
```

然后这里我使用 `vscode` 进行全局搜索 `tars_start.sh` 这个文件名。

![Image.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509185535.png)

从中我们发现其实是使用了常量 `TARS_SCRIPT` 这个变量，所以我们再搜索这个变量。

![Image 2.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509185554.png)

然后我们就会发现 `save2file` 函数就是保存 `tars_start.sh` 的脚本，然后我们可以推测这个代码中是否还存在着具体文件逻辑写入。

![Image 3.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509185609.png)

然后我在前面就找到了如下的代码，对比发现知道，就是从这里面进行编写的服务启动脚本。


![Image 4.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509185626.png)

![Image 5.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509185643.png)
