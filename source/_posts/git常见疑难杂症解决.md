---
title: git常见疑难杂症解决
tags: git
categories: git
date: 2020-10-08 14:14:21
---

 <center>Author：闫玉良</center> 

`git` 如何撤销 `add`、`commit` 操作？如何删除远程分支？...

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

1.`git` 删除远程的分支

```shell
git push origin :分支名
```

2.`git` 文件名大小写变更后未产生变化问题处理

>   原因是默认git配置了忽略大小写敏感

```shell
$ git config core.ignorecase
true
# 下面设置大小写敏感为敏感
$ git config core.ignorecase false
# 这时，再通过git status就能找到你修改文件名大小写后的变更了
```

3.`git` 解决冲突

```shell
git checkout master

git pull

# 假设 f 分支冲突，我们再创建一个 new_f 的分支
git checkout -b new_f

git merge f

# 最后查看一下文件状态，将冲突的文件进行修改，最后再次提交
git status
git add .
git commit -m ’new comment’

# gitlab 发起 merge request 请求
git mr

# mr 是创建了快捷命令，需要在 ~/.gitconfig 中添加如下的代码即可（代码中还添加了其他的快捷命令）
[push]
    default = current
[alias]
    co = checkout
    br = branch
    mwps = push -o merge_request.create -o merge_request.target=master -o merge_request.merge_when_pipeline_succeeds
    mr = push -o merge_request.create -o merge_request.target=master
    stash = stash --include-untracked
```

4.撤销 `commit`

```shell
git reset --soft HEAD^
```

5.撤销 `add`

```shell
git reset HEAD
```

6.撤销工作区内容

```shell
git checkout -- 文件名
```

7.将本地仓库推送到远程已存在仓库

```shell
# 关联远程仓库（第一次使用需要添加github公钥）
git remote add origin git@github.com:***/vue2.1-sell.git
# 或者
git remote add origin https://github.com/***/vue2.1-sell.git

# 获取远程库与本地同步（远程仓库不为空需要这一步）
git pull --rebase origin master

# 把本地内容推送到远程库 使用 git-push
git push -u origin master
```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***