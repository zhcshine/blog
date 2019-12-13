---
title: mysql主从复制
tags:
  - mysql
url: 45.html
id: 45
comments: false
categories:
  - 运维
date: 2016-11-09 14:34:49
---

### 测试环境

主服务器  
192.168.0.160 mysql 5.6.17 win2008R2  
从服务器  
192.168.0.206 mysql 5.6.12 win2003

### 主服务器创建数据库test1, test2

    mysql>CREATE DATABASE `test1` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;     
    mysql>USE test1
    mysql>CREATE TABLE `test1`.`user` ( `id` INT(10) NOT NULL AUTO_INCREMENT , `name` VARCHAR(100) NOT NULL , PRIMARY KEY (`id`)) ENGINE = MyISAM;  
    mysql>INSERT INTO `user` (`id`, `name`) VALUES (NULL, 'user1'), (NULL, 'user2')
    

    mysql>CREATE DATABASE `test2` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;    
    mysql>USE test2
    mysql>CREATE TABLE `test2`.`user` ( `id` INT(10) NOT NULL AUTO_INCREMENT , `name` VARCHAR(100) NOT NULL , PRIMARY KEY (`id`)) ENGINE = MyISAM;  
    mysql>INSERT INTO `user` (`id`, `name`) VALUES (NULL, 'user1'), (NULL, 'user2')
    

### 将test1,test2 复制到从服务器上

    mysql>CREATE DATABASE `test1` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;     
    mysql>USE test1  
    mysql>set names utf8;  
    mysql>source c:/test1.sql;
    mysql>CREATE DATABASE `test2` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;     
    mysql>USE test2  
    mysql>set names utf8;  
    mysql>source c:/test2.sql;
    

### 主服务器配置

编辑配置文件my.ini

    [mysqld] 
    server-id=1     # 配一个唯一的ID编号，1至32  
    log-bin=mysql-bin  # 二进制文件存放路径  
    binlog-do-db=test1  # 需要同步的数据库名  
    binlog-ignore-db=test2  # 不需要同步的数据库名
    

重启mysql并重新登陆，创建授权用户

    mysql>grant REPLICATION SLAVE,RELOAD,SUPER on *.* to slave@192.168.0.206 identified by '123456' ;  
    mysql>grant REPLICATION SLAVE,RELOAD,SUPER on *.* to slave1@192.168.0.118 identified by '123456' ; # 如果是多从
    mysql>flush privileges;     
    mysql>show master status;  # 找到File 和 Position 的值记录下来    
    

![](http://7xq3ry.com1.z0.glb.clouddn.com/20160401152254.png)

### 从服务器配置192.168.0.206

编辑配置文件my.ini添加

    [mysqld]
    server-id=2     #唯一
    replicate-do-db=test1
    replicate-ignore-db=test2
    

重启数据库并重新登陆

    mysql>mysql -uroot -p  
    mysql>change master to master_host='192.168.0.160',master_user='slave',master_password='123456', master_log_file='mysql-bin.000001',master_log_pos=199;  
    mysql>start slave;
    mysql>show slave status\G
    

结果出现一下内容表示成功

    Slave_IO_Running: Yes
    Slave_SQL_Running: Yes
    

![](http://7xq3ry.com1.z0.glb.clouddn.com/20160401152538.png) 多从，另一个从服务器配置192.168.0.118  
编辑配置文件my.ini添加

    [mysqld]
    server-id=3     #唯一，注意和其他的从服务器不一致
    replicate-do-db=test1
    replicate-ignore-db=test2
    

重启mysql并重新登陆，创建授权用户

    mysql>change master to master_host='192.168.0.160',master_user='slave1',master_password='123456', master_log_file='mysql-bin.000001',master_log_pos=199;  
    mysql>start slave;
    mysql>show slave status\G    
    

### 最后测试

在主服务器添加表和数据，可以看到从服务器数据库已经修改。  
重启主从数据库，依然可以正常同步。

### 错误处理办法

连接不上master：  
1，密码是否正确，是否给予权限  
2，防火墙是否开启3306端口 配置的时候注意锁表

    LOCK TABLES
    

    UNLOCK TABLES;
    

错误提示 Got fatal error 1236 from master when reading data from binary log: 'Slave can not handle replication events with the checksum that master is configured to log; the first event 'mysql-bin.000002' at 120, the last event read from './mysql-bin.000002' at 120, the last byte read from './mysql-bin.000002' at 120 由于主库是5.6, 从库是5.5.  
5.6的版本中加入了replication event checksum，主从复制时间校验功能，所以需要把这个关掉才能正常同步到5.5的slave 修改主库

    sudo vim /etc/my.cnf
    binlog_checksum=none
    

重新配置主从即可