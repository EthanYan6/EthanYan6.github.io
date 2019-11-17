---
title: crontab 执行python脚本不生效？（续）
date: 2019-11-17 15:17:31
tags:
- python
- Linux
- crontab
- 定时任务
categories:
- Linux
---

 <center>Author：闫玉良</center> 
Linux 设置定时任务，crontab 执行 python 脚本不生效的问题：

尝试方法：

1.给 python 脚本第一行添加如下代码：

```python
#!/usr/bin/python
```
> 当然 python 解释器的路径要按照自己情况进行填写。

2.如果 python 脚本报错，模块未找到等等，尝试如下方式：
```python
import sys
sys.path.append('xxx<第三方模块的路径>')
```
> 如果是虚拟环境，有可能此路径不同，可以通过下列命令查看：
>```python
>import elasticsearch
>elasticsearch.__file__
>```

然后将路径复制到 `sys.path.append('')` 中的引号即可，如：

```python
D:\\E\\python_virtual\\fbl5n\\lib\\site-packages
```
3.python 脚本中不涉及复杂的逻辑，简单的函数即函数调用即可，如果出现下列代码，请去掉：
```python
if __name__ == '__main__':
```
它会将 crontab 在调用的时候搞晕。

4.可以写一个 shell 脚本去调用 python 脚本，如下：

```shell
#!/usr/bin/bash
cd /opt
/usr/bin/python3 mail_reminder.py >> log.txt 2>&1
```
> 0 表示stdin标准输入
1 表示stdout标准输出
2 表示stderr标准错误
& 表示等同于的意思
2>&1 表示2的输出重定向等同于1

5.当报错编码问题时可以尝试使用如下方法：
```shell
#!/usr/bin/bash 
cd /opt 
PYTHONIOENCODING=utf-8 /usr/bin/python3 mail_reminder.py >> log.txt 2>&1
```
