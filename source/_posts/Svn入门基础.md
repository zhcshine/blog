---
title: Svn入门基础
date: 2019-09-09 13:28:47
tags:
- Svn
categories:
- 运维
---

## 安装

### 服务器端anzhaung

[官方推荐](https://help.ubuntu.com/lts/serverguide/subversion.html.en/)

### Ubuntu安装
```shell
# 安装包
sudo apt install subversion apache2 libapache2-svn
```

### ArchLinux安装
```shell
# 安装
$ sudo yum install subversion
# 查看svn目录
$ which svnserve
```

### 重启

```shell
# 关闭并重启
sudo killall svnserve
svnserve -d -r ~/svn
```
```shell
svn --username=username co svn://svn.xxxx.com/repo0  # 克隆项目
```

## 仓库

### 创建

```shell
$ mkdir ~/svn
$ svnadmin create ~/svn/repo0  # 创建repo0仓库
```

### 配置

```shell
# 配置repo0
$ vim ~/svn/repo0/conf/svnserve.conf
anon-access = none
auth-access = write
password-db = ../../passwd  # 密码文件地址
authz-db = ../../authz      # 权限认证文件地址
```

### 密码

```shell
# 配置密码文件
$ vim ~/svn/passwd
### This file is an example password file for svnserve.
### Its format is similar to that of svnserve.conf. As shown in the
### example below it contains one section labelled [users].
### The name and password for each user follow, one account per line.
[users]
test = testpassword
dev = devpassword
product = productpassword
ui = uipassword
```

### 权限
```shell
# 配置权限认证文件
$ vim ~/svn/authz

[aliases]
# joe = /C=XZ/ST=Dessert/L=Snake City/O=Snake Oil, Ltd./OU=Research Institute/CN=Joe Average

[groups]
# harry_and_sally = harry,sally
# harry_sally_and_joe = harry,sally,&joe
dev = dev
ui = ui
product = product
test = test

# [/foo/bar]
# harry = rw
# &joe = r
# * =

# [repository:/baz/fuz]
# @harry_and_sally = rw
# * = r

[repo0:/]
dev = r
test = r
product = wr
ui = wr

```