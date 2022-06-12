---
title: 常用linux命令
date: 2022-06-10 14:40:11
tags: linux
---

排查当前有哪些进程在使用某个端口
![img.png](img.png)

``` shell
lsof -i:4000
```
![img_1.png](img_1.png)
``` shell
kill -9 4000
```




