---
title: mysql入门基础
tags:
  - mysql
url: 42.html
id: 42
comments: false
categories:
  - 运维
date: 2016-11-09 14:32:49
---

# 安装mysql

```shell
// ubuntu16.04
sudo apt-get install mysql-server
sudo apt install mysql-client  # 服务器端不需要
mysql_secure_installation
```

# 查看mysql版本信息
```shell
mysql --version  
mysql> show variables like '%version_%';
```

# 数据库创建备份导入命令
```sql
// 创建数据库  
CREATE DATABASE `test` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;  

// 删除数据库  
 DROP DATABASE IF EXISTS `test`; 

// 创建表  
CREATE TABLE test(name varchar(32) not null,password varchar(64) not null)DEFAULT charset=utf8;  

### 重置自增字段
ALTER TABLE `tableName` AUTO_INCREMENT = 1; 
或者
truncate tableName; 

// 删除表
DROP TABLE test;
    
// 删除表内容，保留表结构  
mysql> TRUNCATE TABLE `tabel_name`;
    
// 删除表并重置主键，不会留下日志，相当于删除表并重建    
TRUNCATE TABLE `animal_cell_attribute` 
```
```shell
// 导出一个数据库    
mysqldump -u 用户名 -p 数据库名 > 导出的文件名.sql   
mysqldump -u root -p uscnerp > uscnerp.sql    
Enter password:  
    
// 导出一张表
mysqldump -u 用户名 -p 数据库名 表名 > 导出的文件名.sql  
mysqldump -u root -p animal animal_service > animal_service.sql
Enter password: **********  
    
// 导出多个数据库
mysqldump  -uroot -p --databases test mysql #空格分隔
    
// 导出到excel表格
mysql   -uroot   -p   -e   "select   *   from   test.table2 "   >   d:a.xsl
    
```
    
```sql
// 导入数据库  

mysql>use 数据库  
mysql>set names utf8; （先确认编码，如果不设置可能会出现乱码，注意不是UTF-8）   
mysql>source d:\wcnc_db.sql
```
    
# 查看表
```sql
// 查看表结构
show create table 表名;
```


# 配置用户权限
```sql
# 重置root密码并查看所有用户权限
 mysql> use mysql;
Database changed
mysql> UPDATE user SET Password = PASSWORD('newpassword') WHERE user = 'root';
Query OK, 3 rows affected (0.02 sec)
Rows matched: 3  Changed: 3  Warnings: 0

mysql> SELECT Host,User,Password FROM mysql.user;
+-----------+--------+-------------------------------------------+
| Host      | User   | Password                                  |
+-----------+--------+-------------------------------------------+
| localhost | root   | *2ED3112AFECE04CEE0AA42619AB00D4498BA3342 |
| 127.0.0.1 | root   | *2ED3112AFECE04CEE0AA42619AB00D4498BA3342 |
| ::1       | root   | *2ED3112AFECE04CEE0AA42619AB00D4498BA3342 |
| localhost |        |                                           |
| localhost | animal | *CC87B1E39E699B64E0B80741B5D1991BC5BE57AC |
+-----------+--------+-------------------------------------------+
5 rows in set (0.00 sec)

// 查看全部用户
SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.user;
    

// 创建用户
CREATE USER username@localhost IDENTIFIED BY 'password';
> username:用户名  
> @localhost：表示用户只能在本地访问  
> password:密码

// 配置权限
GRANT SELECT,INSERT,UPDATE ON database.* TO username@localhost IDENTIFIED BY 'password';  
GRANT ALL PRIVILEGES ON *.* TO user;@localhost 
// GRANT 权限名称 ON 数据库.数据表 TO 用户名@访问地址 IDENTIFIED BY '密码';
    
// 刷新权限列表
flush privileges;
    
// 配置备份用户权限
CREATE USER 'backuper'@'localhost' IDENTIFIED BY 'PASSWORD';
GRANT SELECT, RELOAD, PROCESS, SHOW DATABASES, SUPER, LOCK TABLES, REPLICATION CLIENT, SHOW VIEW, EVENT ON *.* TO 'backuper'@'localhost';
FLUSH PRIVILEGES;

// 开启慢查询日志
```bash 
sudo vim /etc/my.cnf

# slow_query_log
slow_query_log=on
slow-query-log-file=/var/log/mysql/slow.log # 保证此文件属于mysql用户，mysql组，并可写权限
long_query_time=1 # 超过1秒写入慢查询日志
log-queries-not-using-indexes
    
// 查看是否开启mysql慢查询日志
>mysql show variables like "slow_query_log";

// 设置密码过期时间
SET GLOBAL default_password_lifetime = 0;  // 全局用户永不过期
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;  // 单个用户用户过期
ALTER USER 'root'@'localhost' PASSWORD EXPIRE INTERVAL 90 DAY;  // 单个用户
```


# window服务器下自动备份

## 编写脚本
```bash
rem *******************************Code Start*****************************
@echo off
    
set "Ymd=%date:~,4%%date:~5,2%%date:~8,2%"
mysqldump --opt -u root --password=abcdefg database > F:\FTP\BackupMysql\database_%Ymd%.sql
@echo on
rem *******************************Code End*****************************
```

## 定时任务  
注意在操作中填写脚本并在起始于写入脚本所在盘符E:\

## 删除mysql

> 在服务里找到mysql服务，停止使用  
> 控制面板中卸载mysql 注册表文件删除： HKEY\_LOCAL\_MACHINE/SYSTEM/ControlSet001/Services/Eventlog/Application/MySQL 目录删除; HKEY\_LOCAL\_MACHINE/SYSTEM/ControlSet002/Services/Eventlog/Application/MySQL 目录删除; HKEY\_LOCAL\_MACHINE/SYSTEM/CurrentControlSet/Services/Eventlog/Application/MySQL 目录删除; 在硬盘和注册表中搜索mysql并删除掉
