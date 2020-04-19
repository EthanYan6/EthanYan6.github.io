---
title: Hadoop学习笔记(四)之YARN
date: 2020-04-19 14:03:13
tags: hadoop
categories: hadoop
---

<center>Author：闫玉良</center>

 `Hadoop` 之 `YARN`，你了解吗？

官方文档汇总：https://www.pythonnote.cn/OfficialDocuments/

> 包含了后端常使用的各种文档，小闫静心制作，快收藏起来吧 ~

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

# 1.YARN

## 1.1 背景

`YARN` 的出现是为了解决在 `Hadoop1.x` 版本中存在的一些问题。

之前，`MapReduce` 是 `Master/Slave` 结构，也就是集群中一个 `Job Tracker` 多个 `Task Tracker` 。 `Job Tracker` 负责资源管理和作业调度，`Task Tracker` 负责定期向 `Job Tracker` 报告节点的状态（节点死活，资源使用情况、任务执行情况）以及接收 `Job Tracker` 的命令来执行。不知你是否发现，**问题就出现在这一个 `Job Tracker` 上，它挂掉，整个集群都完蛋。而且它由于负责了所有节点的  `RPC` 请求，压力可想而知，也因此成为了节点规模扩大的瓶颈。最后一点便是集群仅支持 `MapReduce`，不支持其他计算框架**。如果想使用 `Spark` 呢？对不起，再搭建一个集群，想使用 `HBase` 只能再搭建一个集群。这样的一堆集群既不好管理，又使得资源利用率极低（一段时间内这个集群忙，那个集群闲），同时跨集群的数据转移更是问题。于是乎，`YARN` 诞生了。

## 1.2 简介

1) `YARN` 全称是 `Yet Another Resource Negotiator` 「另一种资源协调者」。

2) 它是一个通用的资源管理系统。

3) 它的引入提高了集群的利用率，便于资源统一管理调度，在数据共享方面也带来了极大好处。

4) 它是 `Job Tracker` 的替代者。

## 1.3 架构

![YARN 架构](https://gitee.com/Ethanyan/pic_data/raw/master/291587280526_.pic_hd.jpg)

1) `YARN` 由四部分组成：`Client` 、`ResourceManager`(`RM`)、`NodeManager`(`NM`)、`ApplicationMaster`(`AM`)。

2) 采用了 `Master/Slave` 结构。一个 `ResourceManager` 对应多个 `NodeManager`。`Client` 向 `ResourceManager` 提交任务或终止任务。`ApplicationMaster`(`AM`) 由对应的应用程序完成，每个应用程序对应一个 `ApplicationMaster`(`AM`) ，`ApplicationMaster`(`AM`)  向 `ResourceManager` 申请资源用于在 `NodeManager` 上启动相应的任务。`NodeManager`(`NM`) 通过心跳信息向 `ResourceManager` 汇报自身状态信息。`MapTask` 对应的是 `MapReduce` 作业启动时产生的任务，`MPITask` 是 `MPI` 框架对应的执行任务。

> `MPI` 是消息传递接口，可以理解为更原生的一种分布式模型

## 1.4 核心组件功能

1) `ResourceManager` ：整个集群只有一个。负责集群资源的统一管理和调度；启动或监控 `ApplicationMaster` （一旦某个 `AM` 出现故障，`RM` 将会在另一个节点上启动该 `AM`）；监控 `NodeManager` ，接收其心跳信息并为其分配任务（一旦某个 `NM` 出故障，标记一下该 `NM` 上的任务，来告诉对应的 `AM` 如何处理）。

2) `NodeManager`：整个集群中有多个，负责单节点资源管理和使用。定时向 `ResourceManager` 汇报节点状态信息；接收并处理来自 `ResourceManager` 的 `Container` 启动或停止的各种命令；处理来自 `ApplicationMaster` 的命令。

3)  `ApplicationMaster` ：每个应用一个，负责应用程序的管理。数据切分；为应用程序或作业向 `ResourceManager` 申请资源（`Container`），并分配给内部任务；与 `NodeManager` 通信以启动或者停止任务；任务监控和容错（在任务执行失败时重新为该任务申请资源以重启任务）；处理 `ResourceManager` 发过来的命令：终止 `Container`、让 `NodeManager` 重启等。

4) `Container`：对任务运行环境的抽象。任务运行资源（节点、内存、CPU）；任务启动命令；任务运行环境；任务是运行在 `Container` 中，一个 `Container` 中既可以运行 `ApplicationMaster`，也可以运行具体的 `Map`、`Reduce`、`MPI`、`Spark Task`。

## 1.5 工作原理

![YARN工作原理](https://gitee.com/Ethanyan/pic_data/raw/master/album_temp_1587282871.PNG)

1) 用户向 `YARN` 中提交应用程序/作业，其中包括 `ApplicationMaster` 程序、启动 `ApplicationMaster` 的命令、用户程序等。

2) `Resource Manager` 为作业分配第一个 `Container`，并与对应的 `NodeManager` 通信，要求它在这个 `Container` 中启动该作业的 `ApplicationMaster`。

3) `Application Master` 首先向 `Resource Manager` 注册，这样用户可以直接通过 `Resource Manager` 查询作业的运行状态；然后将为各个任务申请资源并监控任务的运行状态，直到运行结束。即重复步骤（7）。

4) `Application Master` 采用轮询的方式通过 `RPC` 请求向 `ResourceManager` 申请和获取资源。

5) 一旦 `ApplicationMaster` 申请到资源，便与对应的 `NodeManager` 通信，要求它启动任务。

6) `NodeManager` 启动任务。

7) 各个任务通过 `RPC` 协议向 `ApplicationMaster` 汇报自己的状态和进度，以便 `ApplicaitonMaster` 随时掌握各个任务的运行状态，从而可以在任务失败时重新启动任务；在作业运行过程中，用户可随时通过 `RPC` 向 `ApplicationMaster` 查询作业当前运行状态。

8) 作业完成后，`ApplicationMaster` 向 `ResourceManager` 注销并关闭自己。

## 1.6 特点

1) `ResourceManager` 基于 `ZooKeeper` 实现高可用机制，避免发生单点故障。

2) `Node Manager` 执行失败后，`ResourceManager` 将失败任务告诉对应的 `ApplicationMaster`，由 `ApplicationMaster` 决定如何处理失败的任务。

3) `Application Master` 执行失败后，由 `ResourceManager` 负责重启 `ApplicationMaster` 需处理内部任务的容错问题，并保存已经运行完成的 `Task`，重启后无需重新运行。


> 学习自《基于Hadoop与Spark的大数据开发实战》

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

