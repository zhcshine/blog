---
title: django入门基础
tags:
  - django
  - python
url: 47.html
id: 47
comments: false
categories:
  - 后端
date: 2016-11-09 14:41:04
---

### 2017.11.10号重新更新基本安装和线上部署

环境：python3.5 django1.11

    # 安装环境
    $ sudo apt-get update
    $ sudo apt-get install python3-pip apache2 libapache2-mod-wsgi-py3
    $ sudo pip3 install virtualenv
    

    # 安装项目目录
    $ cd ~/www/html/
    $ virtualenv --no-site-packages -p /usr/bin/python3.5 CCO
    $ cd CCO
    $ source ./bin/activate
    (CCO) $ pip3 install ipython
    (CCO) $ pip3 install Django==1.11.7
    # 查看django是否安装成功
    (CCO) $  ipython
     > import django
     > django.VERSION # 打印出django版本表示成功
    # 开始创建项目
    (CCO) $ django-admin.py startproject CCO_WEB
    # 创建应用
    (CCO) $ cd CCO_WEB
    (CCO) $ django-admin.py startapp polls
    

    # 修改文件内容
    (CCO) $ vim CCO_WEB/CCO_WEB/setting.py
    ALLOWED_HOSTS = ['127.0.0.1', 'localhost']  # 修改
    STATIC_URL = '/static/' # 原始
    STATIC_ROOT = os.path.join(BASE_DIR, 'static/') # 添加
    

    # 创建数据库和超级管理员
    (CCO) $ ./manage.py makemigrations
    (CCO) $ ./manage.py migrate
    (CCO) $ ./manage.py createsuperuser # 创建超级管理员
    (CCO) $ ./manage.py collectstatic # 收集静态文件
    

    ### 用django自带服务器运行
    (CCO) $ sudo ufw allow 8000 # 关闭防火墙
    (CCO) $ ./manage.py runserver 0.0.0.0:8000 # 成功表示django开发环境配置完成
    

    ### 线上部署
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
    

    ### 线上
    sudo a2ensite www.cco.com
    sudo service apache2 restart
    http://127.0.0.1 查看
    原文查看　https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-apache-and-mod_wsgi-on-ubuntu-16-04
    

### 数据库安装

    $ sudo apt-get install python-setuptools
    $ sudo apt-get install libmysqld-dev
    $ sudo apt-get install libmysqlclient-dev
    $ sudo apt-get install python-dev
    $ sudo easy_install mysql-python
    $ pip3 install mysqlclient
    

### 安装

    # 需要先安装虚拟环境
    (Novel) pip isntall django==1.8.2 # 不能加sudo
    

### 查看是否安装成功及版本号

    python
    import django
    django.VERSION
    

### 安装MysqlDb

ubuntu

    # ubuntu安装步骤：
    sudo apt-get install python-setuptools
    sudo apt-get install libmysqld-dev
    sudo apt-get install libmysqlclient-dev
    sudo apt-get install python-dev
    sudo easy_install mysql-python
    

windows下https://pypi.python.org/pypi/MySQL-python/1.2.5 下载.exe一键安装

### 创建项目

    django-admin.py startproject Novel
    

### 创建APP

    python manage.py startapp Heros
    

/Novel/settings.py

    INSTALLED_APPS = (
        ...
        'Heroes',  #这里填写的是app的名称
    )
    

### 命令行调试模式

    python manage.py shell
    

### 运行开发服务器

    cd mysite
    python manage.py runserver 或者
    python manage.py runserver 0.0.0.0:8000
    

### 运行shell脚本方式

    python manager.py shell # 启动脚本环境
    from testapp.models import *  # 导入数据库
    

### 部署线上环境

安装apache模块

    # Python 2
    sudo apt-get install libapache2-mod-wsgi
    
    # Python 3
    sudo apt-get install libapache2-mod-wsgi-py3
    

Novel/Novel/wsgi.py

    import os
    import sys
    sys.path.append('/var/www/html/Novel/')
    sys.path.append('/var/www/html/Novel/Novel/')
    sys.path.append('/var/www/html/Novel/lib/python2.7/site-packages') # 虚拟环境需要添加
    from django.core.wsgi import get_wsgi_application
    
    
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "Novel.settings")
    application = get_wsgi_application()
    

/etc/apache2/sites-enable/novel.conf

    <VirtualHost *:1200>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/Novel/Novel/Novel
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    
        # 静态文件设置
        Alias /static/ /var/www/html/Novel/Novel/Novel/static/
        <Directory /var/www/html/Novel/Novel/Novel/static/>
            Require all granted
        </Directory>
    
        WSGIScriptAlias / /var/www/html/Novel/Novel/Novel/wsgi.py
        <Directory /var/www/html/Novel/Novel/Novel/>
            Require all granted
            <Files wsgi.py>
                Require all granted
            </Files>
        </Directory>
    
    </VirtualHost>
    

### 视图与路由绑定

/Heroes/views.py

    from django.http import HttpResponse, Http404
    # Create your views here.
    
    
    def hello(request):
        return HttpResponse("Hello World")
    

/Novel/urls.py

    from django.conf.urls import include, url
    from Heroes.views import *
    
    urlpatterns = [
        url(r'^hello/$', hello),
        url(r'^time/plus/(\d{1,2})/$', hours_ahead)
    ]
    
    

### 视图与模板

/Novel/settings.py设置模板路径

    TEMPLATES = [
        {
            # .....
            'DIRS': [
                os.path.join(os.path.dirname(__file__), 'templates').replace('\\','/'),
            ],
            # ....
        },
    ]
    

/Novel/Heroes/views.py修改视图

    from django.shortcuts import render_to_response
    from django.http import HttpResponse, Http404
    import datetime
    # Create your views here.
    
    
    
    def current_datetime(request):
        now = datetime.datetime.now()
        # html = '<html><body>It is %s.</body></html>' % now
        return render_to_response('Heroes/current_datetime.html', {'current_time': now})
    

/Novel目录下创建templates/Heroes/current_datetime.html

    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Document</title>
    </head>
    <body>
        <h1>{ { current_time }}</h1>
    </body>
    </html>
    

### 数据库配置

/Novel/settings.py

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'Novel',
            'USER': 'root',
            'PASSWORD': 'password',
            'HOST': '127.0.0.1',
            'PORT': '3306'
        }
    }
    

测试是否成功

    python manage.py shell
    >>> from django.db import connection
    >>> cursor = connection.cursor()
    

### 视图返回json数据格式

views.py

    from django.http import HttpResponse, Http404
    from json import dumps
    
    def login_handle(request):
        if request.method != 'POST':
            raise Http404()
        inputs = request.POST
        json_str = dumps(inputs)
        return HttpResponse(json_str, content_type='application/json')
    

\### 静态文件 settings.py ``` # 线上模式静态地址 STATIC_URL = os.path.join(os.path.dirname(**file**), '/static/').replace('\\','/')

开发模式静态地址
========

STATICFILES_DIRS = (os.path.join(os.path.dirname(**file**), 'static').replace('\\','/'),) ```