---
title: Python基础及内建函数学习
tags:
  - python
url: 34.html
id: 34
comments: false
categories:
  - 后端
date: 2016-11-09 10:46:24
---

### 源码安装python2.7.9

    $ sudo apt-get install -y openssl
    $ sudo apt-get install -y libssl-dev # 即 openssl-devel
    $ sudo apt-get install -y libffi-dev # 即 libffi-devel
    $ sudo apt-get install -y python-openssl
    $ sudo apt-get install libbz2-dev
    $ sudo apt-get install libxml2-dev
    $ sudo apt-get install libxslt1-dev
    $ sudo apt-get install sqlite3 libsqlite3-dev
    $ sudo apt-get install libmysqlclient-dev
    

    wget -c https://www.python.org/ftp/python/2.7.9/Python-2.7.9.tgz  
    tar -xzvf Python-2.7.9.tgz  
    cd Python-2.7.9/ 
    

修改源码，支持ssl

    vim Modules/Setup.dist
    

    # Socket module helper for SSL support; you must comment out the other
    # socket line above, and possibly edit the SSL variable:
    #SSL=/usr/local/ssl
    #_ssl _ssl.c \
    #        -DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \
    #        -L$(SSL)/lib -lssl -lcrypto
    

改成

    # Socket module helper for SSL support; you must comment out the other
    # socket line above, and possibly edit the SSL variable:
    SSL=/usr/local/ssl
    _ssl _ssl.c \
            -DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \
    
            -L$(SSL)/lib -lssl -lcrypto
    

    LDFLAGS="-L/usr/lib/x86_64-linux-gnu" ./configure  
    make  
    sudo make install
    

### 虚拟环境搭建

安装

    sudo apt-get install python-virtualenv
    

创建虚拟环境

    cd ~/var/www
    virtualenv --no-site-packages -p /usr/local/bin/python Novel # 参数表示不安装系统已经安装的第三方包, -p表示用哪个版本的python
    

启用虚拟环境

    cd /var/www/Novel
    source ./bin/activate
    

虚拟环境安装包

    (Novel) pip isntall django==1.8.2 # 不能加sudo
    

退出虚拟环境，进入正常环境

    deactivate
    

虚拟环境复制

    pip freeze > requirements.txt
    pip install -r requirements.txt
    

### pip安装超时

    pip --default-timeout=100 install -U pip
    

### 安装python-mysql

    sudo apt-get install python-setuptools
    
    sudo apt-get install libmysqld-dev
    
    sudo apt-get install libmysqlclient-dev
    
    sudo apt-get install python-dev
    
    sudo easy_install mysql-python
    

在终端输入以下内容,不报错则表示成功

    import MySQLdb
    

### 获取文件修改时间

    import time
    filemt= time.localtime(os.stat(filename).st_mtime)  #文件修改时间
    filect = time.localtime(os.stat(filename).st_ctime)  #文件创建
    print time.strftime("%Y-%m-%d",filemt)  
    print time.strftime("%Y-%m-%d",filect)
    

### 获取多少天以前的时间

    import datetime
    # 30天以前的日期
    date_time = (datetime.datetime.now() - datetime.timedelta(days=30)).strftime("%Y-%m-%d")
    print date_time
    

### 去除列表重复项内容

    # 如果列表里面内容为字典
    f = lambda x,y:x if y in x else x + [y]
    products = reduce(f, [[], ] + products_query)
    print products
    
    # 如果列表里面内容为字符串
    l1 = ['b','c','d','b','c','a','a']
    l2 = list(set(l1))
    print l2
    # 保持原来的顺序
    l1 = ['b','c','d','b','c','a','a']
    l2 = sorted(set(l1),key=l1.index)
    print l2
    

### md5，hash加密

    import hashlib
    data =  'This a md5 test!'
    hash_md5 = hashlib.md5(data)  # md5
    hash_md5.hexdigest()
    
    hashlib.sha1('This is a sha1 test!').hexdigest() # sha1
    

### 打印输出对象

    from pprint import pprint
    pprint(vars(object))
    

windows安装python相关环境
===================

### 安装虚拟环境

    # 安装包
    pip install virtualenv
    # 创建虚拟环境env
    virtualenv.exe  env
    # 开启虚拟环境
    ./scripts/active
    # 退出虚拟环境
    deactivate