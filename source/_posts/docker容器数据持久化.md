---
title: docker容器数据持久化
date: 2020-03-06 18:04:08
tags: docker
categories: docker
keywords: docker,持久化,数据卷,数据卷容器
---

<center>Author：闫玉良</center>

`docker` 容器内的数据是独立于镜像之外的，那么如何将其持久化到宿主机呢？答案便是利用「数据卷」，那什么又是「数据卷」呢？

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

# 数据卷

**数据卷其实就是一个目录或者一个文件**，该目录（或文件）可以存在于宿主机上。将此目录（或文件）映射到容器中，便可以持久化容器内的数据到宿主机。如果目录不存在于宿主机上，而是存在于一个容器内部，那么此容器便可以被称为「**数据卷容器**」

下面讲解一下具体的操作方式。

## 1.持久化到目录

数据卷如果是宿主机的一个目录，可以进行如下操作将容器内的数据持久化：

### 1.1 命令介绍

```shell
docker run -itd --name [容器名字] -v [宿主机目录]:[容器目录] [镜像名称] [命令(可选)]
```

该命令**解读**：通过一个镜像启动一个容器。利用 `--name` 指定容器的名称；利用 `-v` 指定宿主机的某目录作为数据卷挂载到容器中，使该容器与宿主机共享此目录 。

> **注意**：1.如果本地路径不存在，`docker` 会贴心的自动创建。2.宿主机的目录请使用**绝对路径**。

### 1.2 操作

1) 执行如下命令，将宿主机的 `/Users/ethanyan/dockerdata/` 目录映射到新创建的 `nginx` 容器中的 `/data` 目录：

```shell
docker run -itd --name nginx -v /Users/ethanyan/dockerdata:/data nginx
```

2) 接下来验证是否生效，先在本地共享目录下创建一个新目录 `NG`：

```shell
cd /Users/ethanyan/dockerdata
mkdir NG
```

3) 进入容器 `nginx` 中看是否存在 `NG` 目录：

```shell
docker exec -it nginx /bin/bash

root@81bbc2c2bf5c:/# cd /data/
root@81bbc2c2bf5c:/data# ls
NG
```

> 可发现在本地创建目录后，容器内的确实也出现了新建的目录。

4) 反向进行操作。在容器的 `/data/NG` 目录中创建一个文件 `testdata.txt` ，退出容器后发现本地也出现了该文件。从而得出**使用此方法可以让容器和宿主机共享目录，并将容器内的数据持久化到本地**。

## 2.持久化到文件

### 2.1 命令介绍

```shell
docker run -itd --name [容器名字] -v [宿主机文件]:[容器文件] [镜像名称] [命令(可选)]
```

> `-d` 是创建容器后，使其在后台持续运行。

### 2.2 操作

1) 执行如下命令，将本地的 `testdata.txt` 文件映射到容器的 `testdata.txt` 文件：

```shell
docker run -itd --name nginx_test -v /Users/ethanyan/dockerdata/NG/testdata.txt:/data/testdata.txt nginx
```

> 同样是创建了一个容器，此容器基于镜像 `nginx` ，并命名为 `nginx_test` 。

**注意：**宿主机要映射的文件与容器内的文件名称可以不同，但是文件类型必须相同！

2) 接下来同样验证一下此操作。在本地的文件 `/Users/ethanyan/dockerdata/NG/testdata.txt` 中写入 `hello world`

```shell
cd /Users/ethanyan/dockerdata/NG/
echo 'hello world' > testdata.txt
```

3) 进入容器内，执行下列操作：

```shell
root@8694fd7cba10:/# cd /data
root@8694fd7cba10:/data# ls
testdata.txt
root@8694fd7cba10:/data# cat testdata.txt
hello world
```

4) 验证了宿主机修改文件后容器内文件也被修改。接下来我们在容器内修改一下内容，看本地是否也被修改：

```shell
docker exec -it nginx /bin/bash

root@8694fd7cba10:/# cd /data
root@8694fd7cba10:/data# ls
testdata.txt
root@8694fd7cba10:/data# echo 'xxx测试用' >> testdata.txt
root@8694fd7cba10:/data# cat testdata.txt
hello world
xxx测试用
root@8694fd7cba10:/data# exit
exit
```

) 然后在本地查看发现内容也被更改。**说明可以使用文件持久化数据**。

## 3.持久化到容器

如果不想这些数据直接暴露在宿主机，可以使用数据卷容器的方式。将数据卷容器挂载到其他容器，就可以多个容器之间共享数据了，而且还可以持久化的保存数据（后面会讲解如何备份和恢复数据卷容器）

**注意：数据卷容器不启动**

### 3.1 步骤

1) 创建数据卷容器

2) 将数据卷容器挂载到其他容器 

**注意：使用数据卷容器是容器之间共享数据，不涉及宿主机，因此数据没有在宿主机上。**

### 3.2 示例

1) 创建一个数据卷容器：

```shell
# 命令
docker create -v [容器数据卷目录] --name [容器名字] [镜像名称] [命令(可选)]
# 示例
docker create -v /data/ --name data nginx
```

> 既然需要创建一个数据卷容器，那么就需要使用镜像，因为我电脑只有一个 `nginx` 镜像，那么使用它即可。一般大家都习惯使用 `centos` 或者 `Ubuntu` 作为基础镜像

2) 创建两个容器（为了测试是否可以在容器间共享数据），并都挂载数据卷容器：

```shell
# 创建容器并挂载数据卷命令
docker run --volumes-from [数据卷容器 id/name] -itd --name [容器名字] [镜像名称] [命令(可选)]
# 示例 
docker run  --volumes-from db847d3fc055 -itd --name nginx_test1 nginx /bin/bash
docker run  --volumes-from db847d3fc055 -itd --name nginx_test2 nginx /bin/bash
# 注意：db847d3fc055 为数据卷容器id
```

3) **验证操作**

先进入容器 `nginx_test1` 中进行操作，创建一个文件：

```shell
docker exec -it nginx_test1 /bin/bash

root@72abfcc9e2e9:/# ls /data/
root@72abfcc9e2e9:/# touch /data/file.txt
root@72abfcc9e2e9:/# ls /data/
file.txt
root@72abfcc9e2e9:/# exit
exit
```

进入容器 `nginx_test2` 验证，并修改文件内容进行反向验证：

```shell
docker exec -it nginx_test2 /bin/bash

root@c5f2c2463640:/# ls /data/
file.txt
root@c5f2c2463640:/# echo 'hello world' > /data/file.txt
root@c5f2c2463640:/# exit
exit
```

然后再进入容器 `nginx_test1` 验证：

```shell
docker exec -it nginx_test1 /bin/bash

root@72abfcc9e2e9:/# ls /data/
file.txt
root@72abfcc9e2e9:/# cat /data/file.txt
hello world
root@72abfcc9e2e9:/# exit
exit
```

### 3.3 备份与恢复

**数据卷容器**的备份与恢复并不是**容器**的备份与恢复，实质上是**数据**的备份与恢复。

> 这句话你品，你细品！

#### 3.3.1 备份

命令：

```shell
docker run -it --volumes-from [数据卷容器id或者name] -v [宿主机存放备份文件的目录]:[容器内存放备份文件目录] [镜像] tar cvf [备份文件路径] [要备份的目录]
```

我们执行示例命令：

```shell
docker run -it --volumes-from data -v /Users/ethanyan/dockerdata/:/backup nginx tar cvf /backup/backup.tar /data
tar: Removing leading `/' from member names
/data/
/data/file.txt
```

该命令是加载数据卷容器并将容器内的 `/data` 目录打包，然后把压缩包保存到 映射到容器内 的新数据卷，本地目录为 `/Users/ethanyan/dockerdata/` 。执行完后，会在本地该目录出现压缩包 `backup.tar`。

**注意：**

1) 备份的命令执行完后会创建出一个临时容器，删除即可。重要的是压缩包。

> 之所以会出现一个临时容器，是因为备份的原理是：创建一个新的容器挂载数据卷容器，而且此容器还与宿主机共享了一个目录（新数据卷），执行打包命令将数据卷容器内的数据打包保存到共享目录中，所以本地会出现一个压缩包。

#### 3.3.2 恢复

请保存好备份的压缩包文件，恢复时用得到。

**步骤：**

1) 创建一个新的数据卷容器 `data_new`

```shell
docker create -v /data/ --name data_new nginx
```

2) 恢复之前的备份文件

```shell
# 命令
docker run --volumes-from [新创建的数据卷容器id或者name] -v [宿主机存放备份文件的目录]:[容器内存放备份文件目录] [镜像] tar xvf [备份文件]

# 执行示例命令
docker run --volumes-from data_new -v /Users/ethanyan/dockerdata/:/backup nginx tar xvf /backup/backup.tar
data/
data/file.txt
```

3) 为了验证是否恢复，我们可以新创建一个容器并挂载新的数据卷容器：

```shell
docker run  --volumes-from data_new -itd --name nginx_test6 nginx /bin/bash

docker exec -it nginx_test6 /bin/bash

root@2b54f0bfefbe:/# ls /data
file.txt
root@2b54f0bfefbe:/# cat /data/file.txt
hello world
```

可看到数据卷容器已经恢复。

#### 3.3.3 备份与恢复的原理

不管是备份还是恢复，我们都使用了一个中间介质，此介质便是一个容器。因为数据卷容器是不启动的，为了备份数据，我们就需要使用一个挂载了此数据卷容器的中间介质（容器）。此介质不光要能连接数据卷容器，还需要连接本地，因此我们又将本地的一个目录映射到了中间介质中。

备份就是中间介质将数据卷容器的目录打包后，再通过映射的目录共享给本地，所以本地会出现一个压缩包。容器列表多了一个新的容器，此容器便是中间介质。

恢复就是中间介质通过映射到容器的共享目录，读取到本地的备份文件（压缩包），然后在容器内部解压缩，数据就恢复到了挂载的数据卷容器中。

> 当然，此中间介质也可以使用 `--name` 参数命名，便于删除。

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***