---
title: crontab定时执行python脚本不成功解决方案
date: 2019-10-31 10:58:36
tags:
- python
- Linux
- crontab
- 定时任务
categories:
- Linux
---

 <center>Author：闫玉良</center> 
目前有一个需求是定时执行某个 python 脚本，但是在 Linux 上设置 crontab 后，不生效？手动执行生效？于是使用下面的方法执行即可：

<!--more-->

大体思路为先写一个 `shell` 脚本，脚本中执行 `python` 文件，然后定时执行 `shell` 脚本即可。具体原因不明，正在探索中 ...... 目前只提供一种解决方案。

1.使用如下命令创建脚本：

```shell
vi start.sh
```

2.然后在 `start.sh` 脚本中结合本身情况输入内容，示例：

```shell
#!/usr/bin/bash
cd /opt
/usr/bin/python3 /opt/test_mail.py
```

3.然后使用如下命令，编辑定时任务：

```shell
crontab -e
```

4.假如设定每天 8 点执行脚本，设置如下：

```shell
0 8 * * * /opt/start.sh
```

5.保存定时任务，几分钟后自动生效。

> **注意**：所有的路径都使用**绝对路径**，否则仍然无效。
