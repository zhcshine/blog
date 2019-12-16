---
title: PostgresSql入门基础
categories:
  - 后端
  - 运维
date: 2018-08-07 13:55:57
tags:
---

# manjaro安装
## 安装postgresql
```
$ git clone https://aur.archlinux.org/postgresql-9.6.git
$ cd postgresql-9.6-git
$ less PKGBUILD  # 查看
$ makepkg -si  # 安装
```

```
# 初始化数据
$ sudo su postgres -l 
$ initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data/'
$ exit
```

```
# 启动及开机启动
sudo systemctl start postgresql.service 
sudo systemctl enable postgresql.service
```

```
# 登录
$ sudo su postgres -l
$ psql
```

## 安装postgis

1, 安装proj4.9

下载源码 [地址](<https://proj.org/download.html>)

```
tar xfz proj-4.9.3.tar.gz
cd proj-4.9.3
./configure
make
sudo make install
# 安装postgis时，如果没有找到库则需要复制动态库
sudo ln -s /usr/local/lib/libproj.so.12 /usr/lib/libproj.so.12
```

2, 安装postgis2.5.2

下载源码 [地址](<https://postgis.net/source/>)

```
wget http://download.osgeo.org/postgis/source/postgis-2.5.2.tar.gz
tar xfz postgis-2.5.2.tar.gz
cd postgis-2.5.2
./configure
make
sudo make install
sudo ldconfig
sudo make comments-install
```
## 创建库
```
# 第一件事是使用\password命令，为postgres用户设置一个密码。
postgres=# \password postgres
# 第二件事是创建数据库用户dbuser（刚才创建的是Linux系统用户），并设置密码。
postgres=# CREATE USER dbuser WITH PASSWORD 'password';
# 第三件事是创建用户数据库，这里为exampledb，并指定所有者为dbuser。
postgres=# CREATE DATABASE exampledb OWNER dbuser;
# 第四件事是将exampledb数据库的所有权限都赋予dbuser，否则dbuser只能登录控制台，没有数据库操作权限。
postgres=# GRANT ALL PRIVILEGES ON DATABASE exampledb to dbuser;
```


# ubuntu/centos安装

[ubuntu安装](https://www.postgresql.org/download/linux/ubuntu/) [centos安装](https://www.postgresql.org/download/linux/redhat/) centos7.2 安装postgresql9.6

    # 添加RPM
    yum install https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-7-x86_64/pgdg-centos96-9.6-3.noarch.rpm
    
    # 安装PostgreSQL 9.6
    # postgresql96-server 数据库核心服务端
    # postgresql96-contrib 附加第三方扩展
    # postgresql96-devel C语言开发Header头文件和库
    yum install postgresql96-server postgresql96-contrib postgresql96-devel
    
    # 验证是否安装成功
    rpm -aq| grep postgres
    

    # 设置目录（可省略）
    cd /mnt
    sudo mkdir vdb1
    sudo chown -R postgres:postgres vdb1
    sudo chmod 700 vdb1
    vi /usr/lib/systemd/system/postgresql-9.6.service
    Environment=PGDATA=/mnt/vdb1/ 修改为自己的新的数据路径
    

    # 初始化数据库
    sudo /usr/pgsql-9.6/bin/postgresql96-setup initdb
    # 开启服务
    sudo service postgresql-9.6 start 或者 systemctl start postgresql-9.6.service
    # 开机启动
    sudo sudo chkconfig postgresql-9.6 on 或者 systemctl enable postgresql-9.6.service
    

    # 配置文件
     /var/lib/pgsql/9.6/data/postgresql.conf 通用设置
     /var/lib/pgsql/9.6/data/pg_hba.conf  控制访问安全
     /var/lib/pgsql/9.6/data/pg_ident.conf 配合pg_hba.conf使用  
    

    sudo su postgres
    psql # 进入命令行模式
    # 修改密码
    ALTER USER postgres WITH PASSWORD '密码'; 
    # 创建用户
    CREATE USER userdoneself WITH PASSWORD '密码passworddoneself';
    # 创建数据库
    create --encoding=UTF8 database dbdoneself owner userdoneself;
    # 赋予权限
    grant all privileges on database dbdoneself to userdoneself;
    \q
    # 安装postgresql时，系统会自动创建postgres用户
    

    # 开启远程访问
    vi /var/lib/pgsql/9.6/data/postgresql.conf 或者  vi /mnt/vdb1/postgresql.conf(创建了目录)
    
    修改#listen_addresses = 'localhost' 为  listen_addresses='*'
    

    # 连接数据库
    先配置登录权限
    vim pg_hba.conf
    # 是否远程登录 数据库 用户名 ip地段 方式
    # 允许doneself用户远程访问
    host doneself doneself 127.0.0.1/32 md5
    # 允许管理员密码登录
    local all postgres  md5
    # 如果不知道怎么配置， 将全部登录方式改成md5
    
    # 查看版本
    /usr/pgsql-9.6/bin/psql --version
    

# 安装postgis2

centos7.2

    sudo yum install postgis2_96  # 96和安装的postgresql一致
    sudo yum install ogr_fdw96  # 安装ogrfdw
    sudo yum install pgrouting_96 # 安装pgrouting
    

    # 用管理员登录postgerysql
    >-- Enable PostGIS (includes raster)
    CREATE EXTENSION postgis;
    -- Enable Topology
    CREATE EXTENSION postgis_topology;
    -- Enable PostGIS Advanced 3D
    -- and other geoprocessing algorithms
    -- sfcgal not available with all distributions
    CREATE EXTENSION postgis_sfcgal;
    -- fuzzy matching needed for Tiger
    CREATE EXTENSION fuzzystrmatch;
    -- rule based standardizer
    CREATE EXTENSION address_standardizer;
    -- example rule data set
    CREATE EXTENSION address_standardizer_data_us;
    -- Enable US Tiger Geocoder
    CREATE EXTENSION postgis_tiger_geocoder;
    
    # 验证是否安装成功
    #postgres=> postgis_full_version();
    

# 安装postgresql中文全文搜索zhparser

centos7.2

     yum install bzip2  # 安装解压包
     # 安装SCWS中文分词 https://github.com/hightman/scws
     wget http://www.xunsearch.com/scws/down/scws-1.2.3.tar.bz2
     tar -jxvf scws-1.2.3.tar.bz2 
     cd scws-1.2.3
     ./configure
     make install
     # 测试
     scws '请问怎么自己动手做西红柿蛋炒饭' -c utf-8
     # 安装zhparser
     git clone https://github.com/amutu/zhparser.git
     cd zhparser
    export PATH=$PATH:/usr/pgsql-9.6/bin/
    cmake && make install
    

    # 登录（第一次登录）
    > sudo su - postgres
    # 修改可以md5登录后
    > > psql -U user -D database -W
    >  输入口令
    # 创建用户
    user=>  CREATE USER dbuser WITH PASSWORD 'password';
    # 修改密码
    user=>  ALTER USER dbuser WITH PASSWORD 'newpassword';
    # 赋予权限
    user=>  GRANT ALL PRIVILEGES ON DATABASE exampledb to dbuser;
    # 允许用户创建数据库
    user=> ALTER USER doneself CREATEDB;
    # 设置为超级管理员
    user=> ALTER ROLE doneself SUPERUSER;
    # 创建数据库
    user=>  Create database exampledb owner=dbuser  template= template1  ENCODING = 'UTF8';
    # 选择数据库
    user=> \c database
    # 查看当前数据库所有表
    user=>  \dt
    user=>  \d 数据库 —— 得到所有表的名字
    user=>  \d 表名 —— 得到表结构