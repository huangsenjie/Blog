---
title: MySQL-server与client的通信方式
date: 2019-12-18 22:36:09
tags: 
 - MySQL
 - cs
categories:
 - MySQL学习笔记
---

# windows上的通信方式
##1. TCP/IP通信
通过TCP/IP是MySQL最常用的MySQL服务器和客户端通信方式，
位于应用层的应用程序要通过TCP/IP协议族通信需要为进程指
定一个端口号。故而，我们需要执行命令
```shell script
mysqld -P3306
```

##2. 命名管道通信
##3. 共享内存通信
##4. 禁止TCP/IP通信
