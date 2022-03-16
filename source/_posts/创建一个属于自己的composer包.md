---
title: 创建一个属于自己的composer包
date: 2022-03-11 21:27:52
tags: composer
categories: 捣鼓小东西
cover: https://cdn.jsdelivr.net/gh/dmf-code/picture/picGo/20220311212849.png
---

#### 初始化

首先建立一个项目目录，然后在项目目录下执行 `composer init`
![](https://cdn.jsdelivr.net/gh/dmf-code/picture/picGo/20220312134237.png)

按照提示信息一步步填写
![](https://cdn.jsdelivr.net/gh/dmf-code/picture/picGo/20220312134432.png)

#### 添加测试代码

`composer` 项目到现在就已经是建立好了，接着我们创建一个 `tests` 目录，用来对项目代码进行测试。

![](https://cdn.jsdelivr.net/gh/dmf-code/picture/picGo/20220312134736.png)

#### 安装 `phpunit` 测试包

```shell
composer require --dev phpunit/phpunit ^9
```

在 `composer.json` 文件中添加以下配置，用于自动加载文件

```json
      "autoload-dev": {
        "psr-4": {
        "Tests\\": "tests/"
        },
        "files": [
        "tests/common.php"
        ]
    }

```

`composer.json` 同级目录下添加 `phpunit.xml` 文件，配置如下

```xml
<phpunit colors="true" bootstrap="./vendor/autoload.php">
    <testsuites>
        <testsuite name="Application Test Suite">
            <directory suffix="Test.php">tests</directory>
        </testsuite>
    </testsuites>

    <filter>
        <whitelist processUncoveredFilesFromWhitelist="true">
            <directory suffix=".php">./src</directory>
        </whitelist>
    </filter>
</phpunit>
```

这里配置了测试驱动 `bootstrap="./vendor/autoload.php"` 根据 `composer` 命名空间进行文件加载。

修改 `composer.json` 后记得执行

```shell
composer dump-autoload -o
```

#### 配置 `phpstorm`

![](https://cdn.jsdelivr.net/gh/dmf-code/picture/picGo/20220312135925.png)

![](https://cdn.jsdelivr.net/gh/dmf-code/picture/picGo/20220312140025.png)

到这里就可以愉快的编写 `composer` 包逻辑了

#### 代码部署到 `github`

在 `github` 上创建仓库
![](https://cdn.jsdelivr.net/gh/dmf-code/picture/picGo/20220314220612.png)

使用第二个已存在仓库推送
![](https://cdn.jsdelivr.net/gh/dmf-code/picture/picGo/20220314222328.png)

在项目目录下初始化 `git`

```shell
git init

git remote add origin git@github.com:dmf-code/basic.git
git checkout -b main
git push --set-upstream origin main
```

> 因为我的 git 初始化项目还是默认 master 为主分支，所以需要改为 main

这样子 `github` 上面就存在我们的代码仓库了。

#### 提交 `github` 项目路径到 `packagist`

复制 `github` 项目路径到 `packagist` 然后提交，这里省略了注册和 `github` 授权部分，不清楚的可以
百度一下。

提交项目完成后，只要再在 `github` 中为项目打个 `tag` 就完成了自己的 `composer` 包了
