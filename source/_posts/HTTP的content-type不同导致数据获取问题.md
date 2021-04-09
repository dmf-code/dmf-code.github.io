---
title: HTTP的content-type不同导致数据获取问题
date: 2021-04-07 18:51:04
tags: 计算机网络
categories: http
cover: https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210409224722.jpeg
---

### 序

今天在前后端对接接口的时候，前端使用 `POST` 请求发送 `t[0][0] = 1`, `t[0][1] = 2` 的参数过来，然后我这边获取到的却是字符串的类型。
```
t[0][0] = "1"

t[0][1] = "2"

t = [["1"], ["2"]];
```

### 问题排查

首先我要排查是否是我这边将二维数组转字符串时出错导致的，所以我这边直接使用
```php

php -r "var_dump(json_encode([[1], [2]]));"

t = [[1], [2]];
```

结果显示是和我转字符串没有关系。然后我直接打印传递过来的参数，确实是 `[["1"], ["2"]]` 这个字符串。所以可以确定的是前端传过来的确实是字符串。

### 知识储备

因为之前有看过 `图解 HTTP` 所以我记得 `content-type` 这个字段不同会导致 `HTTP` 会用不同的方式传递参数。

#### application/x-www-form-urlencoded

![http1.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210407191445.png)

#### multipart/form-data

![http2.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210407191524.png)


#### application/json

![http3.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210407191634.png)

### 总结

从上面的三种请求中，我们可以知道。只有使用 `application/json` 的形式我们才会拿到对应的类型值，使用另外两种都是字符串的形式。

为什么会存在这种问题呢？这里其实是因为第一，第二种以字符串的形式传递过去，但是却没有类型规则限定导致拿到对应字段却无法知道其数据格式，因为拿到的都是字符格式的。然而使用 `json` 却不同，`json` 自身格式限制了字符串都要加上双引号，所以没有双引号的我们就知道是数字类型了。





