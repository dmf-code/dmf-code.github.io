---
title: nginx配置文件
date: 2021-06-24 00:09:19
tags: nginx
categories: nginx
cover: https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210624001106.png
---

### location 中 root 与 alias 的区别

#### 场景

配置 `nginx` 后端访问路径时，发现无法将静态文件识别出来。

使用 `root` 配置方式：
```conf
location /api {
	    root   /root/server/public;
	    try_files $uri $uri/ /index.php?$query_string;
}
```

使用 `alias` 配置方式：
```conf
location /api {
	    alias   /root/server/public;
	    try_files $uri $uri/ /index.php?$query_string;
}
```

#### 问题刨析

`root` 与 `alias` 的区别在于带不带上 `/api`, 使用 `root` 就相当于 `$uri = /root/server/public/api`, 然后 `alias` 就是 `$uri = /root/server/public`。

#### 总结

因为 `root` 默认带上了 `path` 也就是 `location` 后面的 `/api`， 然后静态资源路径就不正确了，所以最后访问到了 `/index.php?$query_string` 路径去了，因为这个文件路径不是合法的路由，导致后台程序处理请求时变为 `404` 了。
