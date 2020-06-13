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
## 1. TCP/IP通信
通过TCP/IP是MySQL最常用的MySQL服务器和客户端通信方式，
位于应用层纄应用程序要通过TCP/IP协议族通信需要为进程指
定一个端口号。故而，我们需要执行命令
```shell script
mysqld -P3306
```
此时客户端默认使用tcp通信
```shell script
mysql -uroot -p
```
或直接指定host和port通过tcp连接到服务器
```shell script
mysql -hlocalhost -P3306 -uroot -p
```
## 2. 命名管道通信
window用户可以用命名管道进行进程间的通信
启动mysql服务器端时指定通信方式
```shell script
mysqld --enable-named-pipe
```
客户端需指定使用命名管道通信连接到服务器
```shell script
mysql -uroot -p --protocol=pipe
```
或
```shell script
mysql -uroot -p --pipe
```
## 3. 共享内存通信
window用户可以用共享内存进行进程间的通信（如果client和server在同一台主机上的话）
启动mysql服务器端时指定通信方式
```shell script
mysqld --shared-memory
```
客户端需指定使用共享内存通信连接到服务器
```shell script
mysql -uroot -p --protocol=memory
```
## 4. 禁止TCP/IP通信
启动server时我们可以禁止client和server通过tcp/ip通信
只需指定参数--skip-networking即可，但此时需同时指定其他通信
方式才可成功启动server
### 1.指定共享内存
```shell script
mysqld --skip-networking --shared-memory
```
此时客户端默认使用共享内存通信
故可以按以下方式连接client和server
```shell script
mysql -uroot -p
```
或
```shell script
mysql -uroot -p --protocol=memory
```
### 2.指定命名管道
```shell script
mysqld --skip-networking --enable-named-pipe
```
此时连接client和server需指定命名管道通信
```shell script
mysql -uroot -p --protocol=pipe
```
或
```shell script
mysql -uroot -p --pipe
```
### 3.同时指定共享内存和命名管道通信
```shell script
mysqld --skip-networking --enable-named-pipe --shared-memory
```
此时默认为共享内存通信
```shell script
mysql -uroot -p
```
或可指定共享内存通信
```shell script
mysql -uroot -p --protocol=memory
```
或可指定命名管道通信
```shell script
mysql -uroot -p --protocol=pipe
```
或
```shell script
mysql -uroot -p --pipe
```

