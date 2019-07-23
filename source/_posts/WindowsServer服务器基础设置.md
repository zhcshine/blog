---
title: WindowsServer服务器基础设置
tags:
  - windows
url: 76.html
id: 76
comments: false
categories:
  - 运维
date: 2016-11-09 16:04:17
---

### 远程连接

禁止administrators组远程连接

> 运行gpedit.msc  
> 计算机配置->windows设置->安全设置->本地策略->用户权限分配->通过终端服务允许登录  
> 将Administrators账号删除掉,保留Remote Desktop Users

创建新的用户

> 本地用户和组->用户右键->新用户->输入用户名，设置密码 添加Administrators组，Remote Desktop Users组

限制登陆次数

> 计算机配置->windows设置->安全设置->账户策略->账户锁定策略”，“账户锁定阈值”设置为 20后，系统会自动设置锁定时间和计数器

3389端口修改

> 修改注册表，是不是需要修改？

超过2个远程连接处理办法及规避方法

    mstsc /console /v:10.10.10.10:3389
    

> 开始－管理工具－终端服务配置－服务器配置－限制每个用户使用一个会话

### 防火墙

开启防火墙

> 控制面板>防火墙>开启  
> 例外里勾选远程桌面,保证服务器可以正常连接  
> 其他情况就在添加端口里添加即可

### FTP

开启ftp

> 在控制面板的添加/删除程序——Windows组件向导——应用程序服务器——Internet信息服务(IIS)中选中“文件传输协议(FTP)服务”。  
> 需要文件夹AMD64 设置一个ftp用户ftpuser  
> 在ftp设置中关掉允许匿名连接  
> 主目录中本地路径为E:\\FTP  
> 不要勾选写入权限  
> 默认ftp站点新建虚拟目录ftpuser 路径E:\\FTP\\ftpuser  
> 在防火墙上开启20，21端口 在ftp客户端选择port主动模式，主动模式不需要服务器开启其他端口