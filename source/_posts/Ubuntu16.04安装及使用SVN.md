---
title: ubuntu16.04安装及使用SVN
url: 570.html
id: 570
comments: false
categories:
  - 办公
  - 后端
  - 运维
date: 2018-10-09 18:21:49
tags:
---

# 服务器端安装

[官方推荐](https://help.ubuntu.com/lts/serverguide/subversion.html.en/)

    # 安装包
    sudo apt install subversion apache2 libapache2-svn
    # 创建目录及两个svn项目
    mkdir svn
    svnadmin create svn/doneself-app
    svnadmin create svn/doneself-web
    # 多个项目公用一个权限文件
    cp -rf svn/doneself/conf/passwd snv/
    cp -rf svn/doneself/conf/authz snv/
    

    # 编辑每个项目的配置文件
    vim svn/doneself/conf/svnserve.conf
    [general]   
    # 匿名用户没有权限
    anon-access = none
    # 验证用户写权限
    auth-access = write
    # 账号密码文件路径
    password-db = ../../passwd 
    # 权限文件路径
    authz-db = ../../authz
    

    # 编写账号密码文件
    vim svn/passwd
    [users] 
    ui = ui  # 注意空格
    

    # 编写组和权限文件
    vim svn/
    [groups]
    ui = ui  
    [doneself-app:/]  
    ui = wr
    

# 启动svn服务器

    # 启动,默认端口号3690
    sudo svnserve -d -r /home/svn
    # 结束
    sudo pkill svnserve