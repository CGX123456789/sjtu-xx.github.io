---
title: mac/linux常用命令
toc: true
date: 2019-11-29 11:08:02
tags: [mac,linux,命令]
categories:
- Linux
---

# 查看指定端口占用，并杀死对应进程
<!--more-->
```shell
lsof: -i:80 #-i表示网络连接，80指明端口号，该命令会同时列出PID
kill -9 1863 # 杀掉pid=1863的进程
```
