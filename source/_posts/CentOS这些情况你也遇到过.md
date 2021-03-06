---
title: CentOS这些情况你也遇到过
date: 2019-12-22 19:15:48
tags:
- Linux
- CentOS
categories:
- Linux
---

<center>Author: 闫玉良</center>
CentOS 虚拟机许久未用，忘记密码如何是好？删掉再来又岂能甘心？想安装某个命令，直接 yum install 就可以了吗？远程连接服务器报错该怎样操作？

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.CentOS 无法登陆情况下修改密码

**背景：**虚拟机忘记 `root` 密码，不知如何登陆

**环境：**`CentOS`，`root` 账户

**操作：**

1.开启虚拟机，在进入系统前选择项页面，按 `e` 键进入系统配置文件

2.向下移动光标，找到 `Linux16` 开头，大概意思如下的一句话：

```shell
Linux16 /vmlinuz-3.10.0xxxxxx.x86_64 root=UUID=xxxxxxxx ro  crashkernel=auto rhgb quiet LANG=zh_CN.UTF-8 
```

3.在该行的最后加空格，输入 `init=/bin/sh`

4.接下来按 `Ctrl + x` 组合键进入单用户模式

> 等待一会，会发现已经进入系统，只不过，命令行前缀变为类似 `sh-4.2#` 的效果

5.输入如下命令：

```shell
mount -o remount,rw /
```

6.之后输入如下命令：

```shell
passwd
```

7.接下来就是修改 `root` 账号密码了

> 重复输入一个不少于8位的密码（密码在输入时是不显示的，只需要正确输入并回车即可）

8.出现 `successfully` 字样表示修改成功，但要生效还需继续操作

9.输入如下命令：

```shell
touch /.autorelabel
```

10.操作完毕，还有一步：

```shell
exec /sbin/init
```

接下来就是等待系统重启（可能需要几分钟，需耐心等待，勿中途关机）

## 2.安装命令小技巧

安装命令，大家一定会想到：

```shell
yum install xxx
```

但并非所有命令都是如此。有些则处于某工具包中，需要下载其基础环境，那该如何操作？

只需执行：

```shell
yum search xxx
```

耐心等待几分钟，直到执行完毕。然后安装最后提示模块即可。

#### 示例

```shell
yum search killall
```

提示：

```shell
Loaded plugins: fastestmirror, security
Loading mirror speeds from cached hostfile

base: mirrors.163.com
extras: mirrors.aliyun.com
*> updates: mirrors.aliyun.com
-----------------------------------Matched:killall-----------------------------------/
psmisc.x86_64 : Utilities for managing processes on your system
```

> 可看到 `killall` 命令处于 `psmisc.x86_64` 中

然后输入：

```shell
yum install psmisc.x86_64
```

## 3.远程连接服务器

**背景：**使用 `ssh` 连接服务器进行相关操作，报错

**环境：**本地 `MacOS`，服务器 `CentOS`

**操作：**

1.使用命令进行远程连接，然后报错如下：

```shell
❱ ssh root@192.168.xx.xx
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:ZBm9L5432tibaN5+weK4h9NJzqrc76lvxOUkVvSLGac.
Please contact your system administrator.
Add correct host key in /Users/ethanyan/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /Users/ethanyan/.ssh/known_hosts:6
ECDSA host key for 192.168.xx.xx has changed and you have requested strict checking.
Host key verification failed.
```

2.使用如下命令进行解决：

```shell
ssh-keygen -R 你要访问的IP地址
```

3.最后再次使用命令进行连接发现连接成功：

```shell
ssh 用户@你要访问的ip地址
```

