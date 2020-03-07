---
title: docker容器网桥连接
date: 2020-03-07 17:56:51
tags: docker
categories: docker
keywords: docker,网桥,容器连接
---
<center>Author：闫玉良</center>
容器之间如何互相通讯？具体的命令有哪些？

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

# 网桥

网桥可以简单的理解为「局域网」，使加入网桥的容器之间可以互相通讯、暴露全部链接，同时与外界又保证了相对隔离的运行环境。

## 1.创建网桥
命令：
```shell
docker network create --driver bridge [网桥名称]
```
> 参数 `--driver bridge` 是默认配置，可写可不写

## 2.连接网桥
连接网桥分为两种方式，一种是在创建容器时链接网桥，一种是在容器运行时链接网桥。

1) 创建容器时：
```shell
docker create --name [容器名称] --network [网桥名称] [镜像名称]
```
2) 容器运行状态时：
```shell
docker network connect [网桥名称] [容器名称]
```
## 3.查看所有网桥
命令：
```shell
docker network ls
```
## 4.查看某网桥的配置信息
命令：
```shell
docker network inspect [网桥id或者名称]
```
## 5.删除自定义网桥
命令：
```shell
docker network rm [网桥id或者名称]
```
***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***
