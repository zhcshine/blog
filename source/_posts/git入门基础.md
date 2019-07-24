---
title: git入门基础
url: 64.html
id: 64
comments: false
categories:
  - 运维
date: 2016-11-09 15:19:56
tags:
---

### 创建项目

    git init
    

### 查看分支

    E:\HKMAIN (master)
    λ git branch
    * master
      v1.1
      v1.2
    λ git branch -v
    * master bf0d2bf 蛋白模板修改USAGE:Reconstitute in sterile ddH2O.
      v1.1   b48a782 试剂盒现货列表添加样本信息一栏
      v1.2   acbf4f1 c1.2
    

### 删除分支

查看未合并的分支

    λ git branch --no-merged
    

删除分支

    git branch -d v1.1
    

如果是未合并的分支，使用大写的-D

    E:\HKMAIN (master)
    λ git branch -d v1.1
    Deleted branch v1.1 (was b48a782).
    
    E:\HKMAIN (master)
    λ git branch -v
    * master bf0d2bf 蛋白模板修改USAGE:Reconstitute in sterile ddH2O.
      v1.2   acbf4f1 c1.2
    

### 创建分支

    git branch v2.0
    

    E:\HKMAIN (master)
    λ git branch Vmysql
    
    E:\HKMAIN (master)
    λ git branch
      Vmysql
    * master
      v1.2
    
    E:\HKMAIN (master)
    λ git checkout Vmysql
    Switched to branch 'Vmysql'
    
    E:\HKMAIN (Vmysql)
    λ
    

或者直接使用git checkout -b v3.0

    E:\HKMAIN (Vmysql)
    λ git checkout -b Vsqlserver
    Switched to a new branch 'Vsqlserver'
    
    E:\HKMAIN (Vsqlserver)
    λ git branch
      Vmysql
    * Vsqlserver
      master
      v1.2
    

### git查看远程仓库

    # git查看远程仓库
    git remote -v
    # git添加远程仓库
    git remote add xxx git@gitlab.xxx.com:xxx/xxx
    # 删除远程分值
    git remote rm xxx
    

### git stash

使用git stash命令先把当前进度保存起来，然后切换到另一个分支去修改bug，修改完提交后，再切回dev分支，使用git stash pop来恢复之前的进度继续开发新功能。

    # 保存当前进度
    git stash
    # 查看现有的存储
    git stash list
    # 恢复最新的进度到工作区
    git stash pop
    git stash save your-file-name