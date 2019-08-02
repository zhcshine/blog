---
title: centos7服务器基础配置
tags:
  - linux
url: 90.html
id: 90
comments: false
categories:
  - 运维
date: 2016-11-10 10:29:42
---

### 查看当前版本

    cat /proc/version 
    cat /etc/redhat-release
    Centos Linux release 7.2.1511 (Core)
    

### 修改root密码

    > passwd root
    > 输入密码
    

### 配置网络

centos网络配置地址 /etc/sysconfig/network Centos设置主机名和网络配置  
/etc/sysconfig/network-scripts/ifcfg-eth0针对特定的网卡进行设置  
/etc/resolv.conf设置DNS  
/etc/hosts设置指定的域名解析地址  
当前使用的是虚拟机网桥模式

    cd /etc/sysconfig/network-scripts/
    vi ifcfg-enp0s23
    # 最后添加内容
    IPADDR=192.168.0.160  # ip地址
    NETMASK=255.255.255.0 # 掩码
    GATEWAY=192.168.0.1 # 网关地址
    DNS1=202.103.24.68 # DNS地址
    # 修改内容
    ONBOOT=yes # 启动时网络接口有效
    BOOTPROTO=none # dhcp改成none dhcp自动获取，static固定，none手动
    

    # 启动网卡
    sudo ifup eth0s23
    sudo systemctl restart network
    

安装网络查看工具

    yum install net-tools
    

centos默认的命令是

    ip addr
    

### 开机需要启动的5个服务

    sshd远程登陆, 
    network网络,
    rsyslog日志相关,
    crond定时任务,
    sysstat检测系统性能
    

### 创建用户

    su
    # 输入密码
    useradd test
    passwd test
    # 输入密码
    # 确认密码
    su test # 切换成普通用户
    

将test用户加入到sudoers用

    #visudo命令 # 相当于直接编辑 /etc/sudoers,但用命令方式更安全,推荐
    su
    cd /etc
    chmod 777 sudoers
    root    ALL=(ALL)       ALL
    test    ALL=(ALL)       ALL  # 新添加的
    chmod 440 sudoers   # 权限改成440，必须，否则会报错
    

### 配置ssh

安装ssh

    yum -y install  openssh*
    

    service sshd start    
    

### ssh连接

    ssh test@192.168.0.160 # 默认22端口
    ssh -p 2222 test@192.168.0.160 # 修改端口后
    

禁止root用户远程ssh连接

    vi /etc/ssh/sshd_congif 
    # 最后添加
    # 禁止root远程登陆  
    PermitRootLogin no
    

### 关闭SELINUX

修改/etc/selinux/config 文件  
将SELINUX=enforcing改为SELINUX=disabled  
setenforce 0 ##设置SELinux 成为permissive模式,临时关闭或者及时起效

### centos开启防火墙

    systemctl mask firewalld
    systemctl stop firewalld
    yum install iptables-services # 如果下面命令报错，则需要安装
    systemctl enable iptables
    systemctl start iptables
    systemctl enable ip6tables
    systemctl start ip6tables
    

防火墙开机启动

    systemctl enable iptables
    systemctl disable iptables
    

    # 查看开机运行的服务
    systemctl list-unit-files --type=service
    

防火墙是否放行ssh端口22

    cd /etc/sysconfig/
    vi iptables
    

在commit前添加以下内容，并注释文件开头信息

    # sample configuration for iptables service
    # you can edit this manually or use system-config-firewall
    # please do not ask us to add additional ports/services to this default configuration
    *filter
    # :INPUT ACCEPT [0:0]
    # :FORWARD ACCEPT [0:0]
    # :OUTPUT ACCEPT [0:0]
    # -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    # -A INPUT -p icmp -j ACCEPT
    # -A INPUT -i lo -j ACCEPT
    # -A INPUT -j REJECT --reject-with icmp-host-prohibited
    # -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    
    
    
    
    
    # 安全设置-防止扫描包
    # NONE 包(所有标识bit都没有设置)主要是扫描类的数据包
    -A INPUT -p tcp --tcp-flags ALL NONE -j DROP
    # 防止sync-flood 攻击
    -A INPUT -p tcp ! --syn -m state --state NEW -j DROP
    # ALL包（所有的标注bit都被设置了）也是网络扫描的数据包
    -A INPUT -p tcp --tcp-flags ALL ALL -j DROP
    
    # 开放对应端口
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
    
    # 缺省设置
    :INPUT DROP [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]
    # 许所有从服务器端发起的连接，由此返回的响应数据应该是允许的,yum操作等
    -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    -A INPUT -p icmp -j ACCEPT
    # 接受主机localhost的任意请求
    -A INPUT -i lo -j ACCEPT
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    
    COMMIT
    
    
    

### ftp

安装及启动

    yum install vsftpd
    systemctl start vsftpd
    systemctl enable vsftpd
    

更新防火墙，放行20， 21端口，如果用了代理上传，需要添加被动模式的端口号  
修改配置项目

    vi /etc/vsftpd/vsftpd.conf
    
    anonymous_enable=No # YES改成No,禁止匿名用户
    listen=YES # No改成Yes 开启监听
    # listen_ipv6=YES  # 此行需要注释掉
    tcp_wrappers=YES   # 默认应该就是YES
    
    # 在/etc/vsftpd.chroot_list文件中列出的用户，可以切换到其他目录；
    # 未在/etc/vsftpd/chroot_list文件中列出的用户，不能切换到其他目录。
    # 需要创建/etc/vsftpd/chroot_list文件
    chroot_local_user=YES
    chroot_list_enable=YES  
    # (default follows) 
    chroot_list_file=/etc/vsftpd/chroot_list
    
    # 防止500oops错误 
    allow_writeable_chroot=YES 
    # 被动模式端口号
    pasv_min_port=30000  
    pasv_max_port=31000    
    
    
    

只允许指定ip登陆

    vi /etc/hosts.allow
    最后添加
    vsftpd:192.168.0.19:allow
    vsftpd:all:deny
    

添加虚拟用户

    useradd -g ftp -d /var/ftp/ftpuser -s /sbin/nologin ftpuser 
    passwd ftpuser
    cd /etc/vsftpd/
    touch chroot_list
    vi chroot_list
    ftpuser # 将用户名称添加到chroot_list里
    

### LAMP安装

apache

    yum install httpd # 安装apache2
    systemctl enable httpd.service
    systemctl enable httpd.service
    

mysql5.6

    # 安装rpm包
    rpm -Uvh http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
    # 查看可以安装的资源
    yum repolist enabled | grep "mysql.*-community.*"
    # 安装
    yum install mysql-community-server
    systemctl start mysqld
    systemctl enable mysqld
    mysql_secure_installation # 设置密码等
    

php5.4

    yum install php
    yum install php-gd php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-snmp php-soap curl curl-devel php-mysql php-devel
    

php5.6或者其他

    yum -y install epel-release
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    wget https://centos7.iuscommunity.org/ius-release.rpm
    rpm -Uvh ius-release*.rpm
    yum -y update
    yum -y install php56u php56u-opcache php56u-xml php56u-mcrypt php56u-gd php56u-devel php56u-mysql php56u-intl php56u-mbstring php56u-bcmath
    

### LAMP配置

apache允许php脚本

    vi /etc/httpd/conf/httpd.conf
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
    AddType application/x-httpd-php .php  # 新添加内容
    

apache虚拟主机

    cd /etc/httpd/conf.d
    vi vhost.conf
    

    <VirtualHost *:121>
    ServerName phpmyadmin.com
    ServerAlias www.phpmyadmin.com
    DocumentRoot /var/www/html/phpMyAdmin/
    ErrorLog /var/www/web_log/phpMyAdmin.www_error.log # 开启日志需要创建web_log文件夹，否则会报错
    CustomLog /var/www/web_log/phpMyAdmin.www_access.log common
    </VirtualHost>
    <Directory "/var/www/html/phpMyAdmin/">
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    
    <VirtualHost *:108>
    ServerName hkmain.com
    ServerAlias www.kmain.com
    DocumentRoot /var/www/html/NEW/
    ErrorLog /var/www/web_log/NEW.www_error.log
    CustomLog /var/www/web_log/NEW.www_access.log common
    </VirtualHost>
    <Directory "/var/www/html/NEW/">
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    

apache加载的模块查看

    apachectl -M | sort
    

网站目录权限配置

    chown -R apache:apache WEB_FOLD
    chmod 750 WEB_FOLD
    cd WEB_FOLD
    find -type d -exec chmod 750 {} \;
    find -not -type d -exec chmod 640 {} \;
    find . -name "Runtime" -type d -exec chmod -R 770 {} \;
    

### LNMP安装与配置

参考链接 https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-centos-7 nginx安装(apache二选一)

    # 安装pcre库，使nginx支持rewrite
    sudo yum install pcre pcre-devel -y
    sudo yum install nginx
    sudo systemctl start nginx.service
    # 设置开机启动
    sudo systemctl enable nginx.service
    

mysql5.6

    # 安装rpm包
    rpm -Uvh http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
    # 查看可以安装的资源
    yum repolist enabled | grep "mysql.*-community.*"
    # 安装
    yum install mysql-community-server
    systemctl start mysqld
    systemctl enable mysqld
    mysql_secure_installation # 设置密码等
    

php安装

    yum -y install epel-release
    sudo yum install php php-mysql php-fpm
    sudo yum install php-pecl-memcache
    

配置

    vim /etc/php.ini
    #cgi.fix_poathinfo=1改成
    cgi.fix_pathinfo=0
    

    vim /etc/php-php.d/www.conf
    # 第一处修改
    listen = /var/run/php-fpm/php-fpm.sock
    # 第二处修改
    listen.owner = nobody
    listen.group = nobody
    # 第三处修改
    user = nginx
    group = nginx
    

    # 重启
    sudo systemctl start php-fpm
    sudo systemctl enable php-fpm
    

    # 安装xcahe
    sudo yum -y install php-xcache xcache-admin
    # 如果失败请用下面的方法
    # xcahe
    wget https://kojipkgs.fedoraproject.org//packages/php-xcache/3.1.1/1.el7/x86_64/php-xcache-3.1.1-1.el7.x86_64.rpm
    # 图形化管理
    wget https://kojipkgs.fedoraproject.org//packages/php-xcache/3.1.1/1.el7/noarch/xcache-admin-3.1.1-1.el7.noarch.rpm
    

#配置nginx sudo vi /etc/nginx/conf.d/default.conf server { listen 80; server\_name server\_domain\_name\_or_IP; # 注意位置，在括号外 root /usr/share/nginx/html; index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
        # 下面是配置fpm
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php-fpm/php-fpm.sock; (这个值从php-fpm复制来)
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    

}

    # 测试
    sudo vi /usr/share/nginx/html/info.php
    <?php echo phpinfo();>
    sudo systemctl restart php-fpm
    sudo systemctl restart nginx
    

### 安装imagemagick

    yum install php-pear gcc
    yum install ImageMagick ImageMagick-devel ImageMagick-perl
    pecl install imagick # 直接回车
    echo extension=imagick.so >> /etc/php.ini # 添加php扩展
    systemctl restart httpd
    

### mysql主从复制主服务器配置

    vi /etc/my.cnf
    [mysqld] 
    server-id=1     # 配一个唯一的ID编号，1至32  
    log-bin=mysql-bin  # 二进制文件存放路径  
    binlog-do-db=test1  # 需要同步的数据库名  
    binlog-ignore-db=test2  # 不需要同步的数据库名 
    

其他参考mysql主从复制

### rsycn数据同步

    yum install rsync # 安装，默认端口873  
    systemctl start rsyncd
    systemctl enable rsyncd
    
    

    vim rsync.conf# 日志文件位置，启动rsync后自动产生这个文件，无需提前创建
    log file = /var/log/rsyncd.log
    # pid文件的存放位置
    pidfile = /var/run/rsyncd.pid
    # 支持max connections参数的锁文件
    lock file = /var/run/rsync.lock
    # 用户认证配置文件，里面保存用户名称和密码，后面会创建这个文件
    secrets file = /etc/rsync.pass
    # rsync启动时欢迎信息页面文件位置（文件内容自定义
    # motd file = /etc/rsyncd.Motd
    #
    
    # 设置rsync运行权限为root
    # uid = root
    # 设置rsync运行权限为root
    # gid = root
    # 匿名用户
    uid = 0
    gid = 0
    # 默认端口
    port = 873
    # 默认为true，修改为no，增加对目录文件软连接的备份
    use chroot = no
    # 设置rsync服务端文件为读写权限
    read only = yes
    # 不显示rsync服务端资源列表
    list = no
    # 最大连接数
    max connections = 200
    # 设置超时时间
    timeout = 600
    # 不验证用户密码
    strict modes = false
    # 执行数据同步的用户名，可以设置多个，用英文状态下逗号隔开
    # auth users = test
    # 允许进行数据同步的客户端IP地址，可以设置多个，用英文状态下逗号隔开
    hosts allow = 192.168.0.206, 192.168.0.118
    # 禁止数据同步的客户端IP地址，可以设置多个，用英文状态下逗号隔开
    # hosts deny = 192.168.21.254
    
    # 自定义名称
    [web]
    # rsync服务端数据目录路径
    path = /var/www/html/HKMAIN_NEW/Upload/product/manual/cn/
    #
    # 模块名称与自定义名称相同
    comment = web
    
    [webe]
    path = /var/www/html/HKMAIN_NEW/Upload/product/manual/en/
    comment = webe
    

### centos7.2安装nfs服务器端

    # 服务器端安装及配置
    # 安装命令
    sudo yum install nfs-utils rpcbind
    # 查看是否安装成功
    $ rpm -aq nfs-utils rpcbind
    rpcbind-0.2.0-38.el7.x86_64
    nfs-utils-1.3.0-0.33.el7_3.x86_64
    # 启动rpcbind
    sudo systemctl restart rpcbind
    # 查看状态
    systemctl status rpcbind
    # 开机启动
    sudo systemctl enable rpcbind
    # 查看是否开机启动
    systemctl is-enabled rpcbind # enable表示开机启动，disable表示不开机启动
    # 启动及开机启动nfs服务
    sudo systemctl restart nfs
    sudo systemctl enable nfs
    #配置nfs服务
    vim /etc/exports
    /readonly 192.168.0.160(insecure,ro,no_root_squash) # 指定ip地址，只读
    /rwsync 192.168.0.0/24(insecure,rw,sync,no_root_squash) # 指定ip地址段，读写
    # 加载配置文件
    sudo exportfs -rv
    # 查看nfs服务器本地挂载情况
    $ showmount -e localhost
    Export list for localhost:
    /rwsync   192.168.0.0/24
    /readonly 192.168.0.160
    # 重启服务
    sudo systemctl restart nfs
    
    
    # 客户端安装配置
    sudo yum install nfs-utils rpcbind
    # 只需启动rpc服务
    sudo systemctl restart rpcbind
    sudo systemctl enable rpcbind
    # 查看可以挂载服务器端的目录
    showmount -e 192.168.0.206
    Export list for 192.168.0.206:
    /rwsync   192.168.0.0/24
    /readonly 192.168.0.160
    # 挂载
    sudo mount -t nfs 192.168.0.206:/readonly /mnt/readonly
    sudo mount -t nfs 192.168.0.206:/rwsync /mnt/rwsync
    # 查看挂载结果
    df -h
    
    
    

### centos7.2安装python3.5

    # 安装编译环境
    yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel
    # 下载文件
    wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tgz
    # 解压
    tar -zxvf Python-3.6.6.tgz
    # 编译安装
    cd Python-3.5.1
    ./configure --prefix=/usr/local/python3
    make && make install
    # 创建软连接
    sudo ln -s /usr/local/python3/bin/python3 /usr/bin/python3
    python3 --version
    sudo ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3 
    pip3 --version
    
    # 添加pip源
    vim ~/.pip/pip.conf
    [global]
    trusted-host =  mirrors.aliyun.com
    index-url = https://mirrors.aliyun.com/pypi/simple
    
    ### 安装虚拟环境
    sudo pip3 uninstall virtualenv
    sudo ln -s /usr/local/python3/bin/virtualenv /usr/bin/virtualenv 
    
    ### 创建虚拟环境
    virtualenv --no-site-packages -p python3 .env
    source ./.env/bin/activate
    

### centos7.2安装node8

    # 参考文件 https://nodejs.org/en/download/package-manager/
    On RHEL, CentOS or Fedora, for Node.js v8 LTS:
    
    curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
    Alternatively for Node.js 10:
    
    curl --silent --location https://rpm.nodesource.com/setup_10.x | sudo bash -
    Then install:
    sudo yum -y install nodejs
    
    
    # 安装cnpm
    npm install -g cnpm --registry=https://registry.npm.taobao.org
    

### centos6.8安装nginx

    # 安装pcre库，使nginx支持rewrite
    yum install pcre pcre-devel -y
    # 安装openssl，https服务需要使用
    yum install openssl openssl-devel -y
    # 安装 gzip 类库
    yum -y install zlib zlib-devel
    # 安装编译程序
    yum -y install make gcc gcc-c++ ncurses-devel
    # 创建用户
    useradd nginx -s /sbin/nologin -M
    # 下载源码
    wget -q https://nginx.org/download/nginx-1.6.3.tar.gz 
    tar -zxvf nginx-1.6.3.tar.gz
    cd nginx-1.6.3
    ./configure --user=nginx --group=nginx --prefix=/usr/local/nginx --with-http_ssl_module --with-http_stub_status_module --with-pcre
    make
    make install
    

    # 启动nginx
    /usr/local/nginx/sbin/nginx
    # 停止nginx
    /usr/local/nginx/sbin/nginx -s quit
    # 重新加载配置文件
    /usr/local/nginx/sbin/nginx -s reload        
    # 测试你的配置文件是否有误
    /usr/local/nginx/sbin/nginx -t       
    # 查看nginx对应的端口
     lsof -i :80
    

### centos6.8安装mysql5.7

    # yum源直接安装的是mysql5.1版本，抛弃
    # 先检查是否有旧版本
    rpm -qa | grep mysql
    # 删除旧版本
    sudo yum -y remove mysql-xx-xx
    wget https://dev.mysql.com/get/mysql57-community-release-el6-10.noarch.rpm
    sudo yum install mysql57-community-release-el6-10.noarch.rpm
    sudo yum install mysql-community-server
    

### centos7.2 源码安装nginx

    # 安装pcre库，使nginx支持rewrite
    yum install pcre pcre-devel -y
    # 安装openssl，https服务需要使用
    yum install openssl openssl-devel -y
    # 安装 gzip 类库
    yum -y install zlib zlib-devel
    # 安装编译程序
    yum -y install make gcc gcc-c++ ncurses-devel
    # 创建用户
    useradd nginx -s /sbin/nologin -M
    # 下载源码
    wget -q https://nginx.org/download/nginx-1.12.1.tar.gz 
    tar -zxvf nginx-1.12.1.tar.gz
    cd nginx-1.12.1
    ./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-http_ssl_module --with-http_realip_module --with-http_gunzip_module --with-http_gzip_static_module --with-threads --with-file-aio --with-ipv6 --with-http_v2_module --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector --param=ssp-buffer-size=4 -m64 -mtune=native'
    make
    make install
    

    # 创建链接
    sudo ln -s /usr/local/nginx/bin/nginx /usr/bin/nginx
    # 启动nginx
    /usr/local/nginx/sbin/nginx
    # 停止nginx
    /usr/local/nginx/sbin/nginx -s quit
    # 重新加载配置文件
    /usr/local/nginx/sbin/nginx -s reload        
    # 测试你的配置文件是否有误
    /usr/local/nginx/sbin/nginx -t       
    # 查看nginx对应的端口
     lsof -i :80
    
    

### centos7.2安装php7

    # 加源
    sudo yum install epel-release yum-utils
    sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
    # 选择remi
    sudo yum-config-manager --enable remi-php70
    sudo yum-config-manager --enable remi-php71
    sudo yum-config-manager --enable remi-php72
    # 安装相关扩展
    sudo yum-config-manager --enable remi-php70
    sudo yum install php php-common php-opcache php-mcrypt php-cli php-gd php-curl php-mysql
    # 安装 PHP FPM 
    sudo yum install php-fpm
    sudo systemctl enable php-fpm
    sudo systemctl start php-fpm
    # 配置
    sudo vim /etc/php-fpm.d/www.conf
    user = nginx
    group = nginx
    

    # 配置nginx虚拟server
    server {
    
        # . . . other code
    
        location ~ \.php$ {
            try_files $uri =404;
            # fastcgi_pass unix:/run/php-fpm/www.sock;
                    fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }
    

### centos7.2安装redis

    $ sudo yum install redis
    $ sudo systemctl enable redis
    $ sudo systemctl start redis
    # 查看版本号
    $ redis-server --version
    

    # 使用方法 http://www.zhuohc.com/articles/redis%E5%9F%BA%E7%A1%80/
    

[centos7.2安装使用docker](/articles/centos7-2安装及使用docker/) [centos7.2安装使用sentry](/articles/centos7-2安装使用sentry/) [centos7.2安装使用rabbitMQ](/articles/centos7-2使用rabbitmq/) [centos7.2安装使用postgresql](/articles/postgresql常见命令/)

#### centos7.2安装supervisor
安装
```shell
$ sudo yum install -y supervisor
$ systemctl enable supervisord # 开机自启动
$ systemctl start supervisord # 启动supervisord服务
$ systemctl status supervisord # 查看supervisord服务状态
$ ps -ef|grep supervisord # 查看是否存在supervisord进程
```
配置
```shell
# 设置web访问
[inet_http_server]
port=*:9001                                                  
username=username
password=password 
[include]                                                                                   │(.env-api) ➜  supervisord.d sudo vim doneself-api.ini                 
files = supervisord.d/*.ini   # 配置文件地址
```
```shell
$ vim xxx.ini
[program:doneself-api-uwsgi]
command = /home/doneself/code/web/.env-api/bin/uwsgi --ini /home/doneself/code/web/api/uwsgi.ini
autostart = true
autorestart = true
stopsignal= QUIT
user= doneself
redirect_stderr=false
stdout_logfile = /home/doneself/code/web/api/log/uwsgi_stdout.log
stderr_logfile = /home/doneself/code/web/api/log/uwsgi_stderr.log
```
[其他信息](http://supervisord.org/)