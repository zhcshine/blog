---
title: Mongodb入门基础
date: 2019-12-03 15:16:10
tags:
-  数据库
-  非关系型数据库
-  Mongodb
categories:
- 运维
---

## 安装Mongodb

### arch linux

```shell
# aur
$ git clone https://aur.archlinux.org/mongodb-bin.git
$ cd mongodb-bin
$ makepkg -si
$ sudo systemctl enable mongodb.service
$ sudo systemctl start mongodb.service
```


## 基础命令

```shell
# 进入终端
$ mongo
MongoDB shell version v4.2.1
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("5ae8192f-e4b1-408f-9795-ea731610d5ad") }
MongoDB server version: 4.2.1
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
        http://docs.mongodb.org/
Questions? Try the support group
        http://groups.google.com/group/mongodb-user
Server has startup warnings: 
2019-12-03T15:18:28.142+0800 I  STORAGE  [initandlisten] 
2019-12-03T15:18:28.142+0800 I  STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
2019-12-03T15:18:28.142+0800 I  STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
2019-12-03T15:18:28.728+0800 I  CONTROL  [initandlisten] 
2019-12-03T15:18:28.728+0800 I  CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2019-12-03T15:18:28.728+0800 I  CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2019-12-03T15:18:28.728+0800 I  CONTROL  [initandlisten] 
2019-12-03T15:18:28.728+0800 I  CONTROL  [initandlisten] 
2019-12-03T15:18:28.728+0800 I  CONTROL  [initandlisten] ** WARNING: soft rlimits too low. rlimits set to 95847 processes, 1048576 files. Number of processes should be at least 524288 : 0.5 times number of files.
---
Enable MongoDB's free cloud-based monitoring service, which will then receive and display
metrics about your deployment (disk utilization, CPU, operation statistics, etc).

The monitoring data will be available on a MongoDB website with a unique URL accessible to you
and anyone you share the URL with. MongoDB may use this information to make product
improvements and to suggest MongoDB products and deployment options to you.

To enable free monitoring, run the following command: db.enableFreeMonitoring()
To permanently disable this reminder, run the following command: db.disableFreeMonitoring()
---
```
```
# 打印当前数据库
$ db
-> test
```
