---
title: Django入门基础
tags:
- Django
- Python
categories:
- 后端
date: 2016-11-09 14:41:04
---

## 环境安装

### Python3.5

 {% post_link Python入门基础 %}

### Django1.11

```shell
# 安装项目目录
$ cd ~/www/html/
$ virtualenv --no-site-packages -p /usr/bin/python3.5 CCO
$ cd CCO
$ source ./bin/activate
(CCO) $ pip3 install ipython
(CCO) $ pip3 install Django==1.11.7
# 查看django是否安装成功
(CCO) $  ipython
$ import django
$ django.VERSION # 打印出django版本表示成功
# 开始创建项目
(CCO) $ django-admin.py startproject CCO_WEB
# 创建应用
(CCO) $ cd CCO_WEB
(CCO) $ django-admin.py startapp polls
```

### 配置Django
```shell
# 修改文件内容
(CCO) $ vim CCO_WEB/CCO_WEB/setting.py
ALLOWED_HOSTS = ['127.0.0.1', 'localhost']  # 修改
STATIC_URL = '/static/' # 原始
STATIC_ROOT = os.path.join(BASE_DIR, 'static/') # 添加
```

### 数据库迁移
```shell
# 创建数据库和超级管理员
(CCO) $ ./manage.py makemigrations
(CCO) $ ./manage.py migrate
(CCO) $ ./manage.py createsuperuser # 创建超级管理员
```

### 静态文件收集
```shell
(CCO) $ ./manage.py collectstatic # 收集静态文件
```

### 本地开发环境
```shelll
# 用django自带服务器运行
(CCO) $ sudo ufw allow 8000 # 关闭防火墙
(CCO) $ ./manage.py runserver 0.0.0.0:8000 # 成功表示django开发环境配置完成
```
  
### Apache线上部署

安装apache模块
```shell
# Python 2
$ sudo apt-get install libapache2-mod-wsgi

# Python 3
$ sudo apt-get install libapache2-mod-wsgi-py3
```
  
```shell
apache虚拟主机配置
$ sudo vim /etc/apache2/sites-available/www.cco.com.conf
<VirtualHost *:80>
        ServerName www.example.com
        ServerAdmin webmaster@localhost
         # 这里指明静态文件存放的地方
    Alias /static  /home/hanchang/www/html/CCO/CCO_WEB/static
    # 给文件夹分配权限
    <Directory /home/hanchang/www/html/CCO/CCO_WEB/static>
            Require all granted
    </Directory>
    #接下来，我们将授予访问存储Django代码的二级项目目录中的wsgi.py文件的权限。
    <Directory /home/hanchang/www/html/CCO/CCO_WEB/CCO_WEB>
    <Files wsgi.py>
            Require all granted
    </Files>
    </Directory>
        WSGIDaemonProcess CCO_WEB python-home=/home/hanchang/www/html/CCO/CCO_WEB/CCO_WEB python-path=/home/hanchang/www/html/CCO
    WSGIProcessGroup CCO_WEB
    WSGIScriptAlias / /home/hanchang/www/html/CCO/CCO_WEB/CCO_WEB/wsgi.py
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
```shell
### 线上
sudo a2ensite www.cco.com
sudo service apache2 restart
http://127.0.0.1 查看
```
[原文查看](https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-apache-and-mod_wsgi-on-ubuntu-16-04)
 
 
 ### 守护进程
 
 {% post_link CentOs7.2服务器基础配置 %} Supervisor部分
 
 