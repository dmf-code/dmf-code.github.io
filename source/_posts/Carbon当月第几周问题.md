---
title: Carbon当月第几周问题
date: 2021-05-09 19:01:26
tags: php
categories: php问题集
cover: https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509190213.png
---

### 序

今天遇到一个问题，那就是使用 `Carbon` 包获取当月的第几周时不准确。

#### 获取方式

```php
Carbon::now()->weekOfMonth
```

#### 结果

```txt
2020-11-29 周日 第5周
2020-11-30 周一 第5周
```

![image.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509190239.png)

观察发现，这两个值不应该相等的。所以这里只能是查看 `Carbon` 里面的具体实现了。

#### 追查源码

```php

case $name === 'weekOfMonth':
     return (int) ceil($this->day / static::DAYS_PER_WEEK);

```

搜索 `weekOfMonth` 我们发现，这个函数只是简单的使用 `29 / 7` 然后向上取整（[ceil函数](https://www.php.net/manual/zh/function.ceil.php)）。所以就会存在 `29 / 7` 和 `30 / 7` 向上取整的结果都是 `5` 的情况。

然后我们可以看见它下面还有一个类似的函数 `weekNumberInMonth` 。

```php

case $name === 'weekNumberInMonth':
     return (int) ceil(($this->day + $this->copy()->startOfMonth()->dayOfWeek - 1) / static::DAYS_PER_WEEK);

```

然后根据这个公式计算的结果还是不符合，出现了返回 `0` 的情况。

在 `github` 上面搜到的结果是[weekNumberInMonth return 0](https://github.com/briannesbitt/Carbon/issues/1879)。

得到的结论就是现在这个版本 `dayOfWeek` 星期日-星期六采用的是 `0 - 6` 表示，然后想要得到正确的显示就需要使用 `dayOfWeekIso` 然后这个值获取的是星期一到星期日，用 `1 - 7` 表示。


最后这里可以更新为最新的包就解决这个问题了，其实这个也不算 `bug` 因为不同的规则定义就存在不同的第几周。
