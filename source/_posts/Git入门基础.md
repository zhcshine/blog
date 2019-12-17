---
title: Git入门基础
categories:
- 运维
tags:
- Git
- Linux
date: 2016-11-09 15:19:56
---

## 配置
```shell
# 设置代理
$ git config --global http.proxy 'socks5://127.0.0.1:1080'
$ git config --global https.proxy 'socks5://127.0.0.1:1080'
# 查看配置项
$ git config --list
```

## 使用

### 初始化
```shell
git init
```
    
### 创建分支
```shell
$ git branch v2.0
E:\HKMAIN (master)
$ git branch Vmysql
E:\HKMAIN (master)
$ git branch
Vmysql
* master
v1.2

E:\HKMAIN (master)
$ git checkout Vmysql
Switched to branch 'Vmysql'

E:\HKMAIN (Vmysql)
$
```

```shell
# 或者直接使用git checkout -b v3.0
E:\HKMAIN (Vmysql)
$ git checkout -b Vsqlserver
Switched to a new branch 'Vsqlserver'
    
E:\HKMAIN (Vsqlserver)
$ git branch
  Vmysql
* Vsqlserver
  master
  v1.2
```

### 查看分支
```shell
E:\HKMAIN (master)
$ git branch
* master
  v1.1
  v1.2
$ git branch -v
* master bf0d2bf 蛋白模板修改USAGE:Reconstitute in sterile ddH2O.
  v1.1   b48a782 试剂盒现货列表添加样本信息一栏
  v1.2   acbf4f1 c1.2
```
    
### 删除分支
```shell
# 查看未合并的分支
$ git branch --no-merged
```

```shell
# 删除分支
$ git branch -d v1.1
```    

```shell
# 如果是未合并的分支，使用大写的-D
E:\HKMAIN (master)
$ git branch -d v1.1
Deleted branch v1.1 (was b48a782).
E:\HKMAIN (master)
$ git branch -v
* master bf0d2bf 蛋白模板修改USAGE:Reconstitute in sterile ddH2O.
  v1.2   acbf4f1 c1.2
```
    
### 远程仓库
```shell
# git查看远程仓库
$ git remote -v
# git添加远程仓库
$ git remote add xxx git@gitlab.xxx.com:xxx/xxx
# 删除远程分值
$ git remote rm xxx
```

### 暂存
```shell
# 使用git stash命令先把当前进度保存起来，然后切换到另一个分支去修改bug，修改完提交后，再切回dev分支，使用git stash pop来恢复之前的进度继续开发新功能。
# 保存当前进度
$ git stash
# 查看现有的存储
$ git stash list
# 恢复最新的进度到工作区
$ git stash pop
$ git stash save your-file-name
```
