---
title: Ubuntu14.04服务器基础配置
tags:
  - ubuntu
url: 95.html
id: 95
comments: false
categories:
  - 运维
date: 2016-11-10 10:49:23
---

# 登陆ssh

    ssh root@ipaddress 
    password:
    

# 首次登陆出现的错误

    perl: warning: Setting locale failed.
    perl: warning: Please check that your locale settings:
    

    # 安装 localepurge 管理语言文件
    sudo apt-get install localepurge
    # 或者之后自行配置
    sudo dpkg-reconfigure localepurge
    # 生成自己需要的语言
    sudo locale-gen zh_CN.UTF-8 en_US.UTF-8
    # 打印结果
    locale
    # ssh连接时候指定语言
    LC_ALL=en_US.UTF-8 ssh <host>
    

# 创建用户

    adduser hanchang
    password:
    retry password:
    

# 将用户添加到sudo用户组

错误提示

    username is not in the sudoers file.
    

    su -
    password：
    chmod u+w /etc/sudoers   # 添加文件写权限
    root ALL=(ALL) ALL 下面添加以下代码
    hanchang ALL=(ALL) NOPASSWD: ALL
    :wq 退出
    chmod u-w /etc/sudoers  # 取消文件写权限
    

修改root密码

    sudo passwd
    password:
    retry password:
    

# 禁止root用户远程登陆ssh

    cd /e   tc/ssh/
    sudo vim sshd_config
    PermitRootLogin yes改成PermitRootLogin no
    service sshd restart    或者 /etc/init.d/sshd restart（ssh服务器重启）
    

# 指定IP地址登陆

    vim  /etc/hosts.allow
    sshd:192.168.220.164:allow  # 最后添加
    
    vim /etc/hosts.deny
    sshd:ALL #最后添加
    

# 避免sshd连接超时

修改客户端

    sudo vim /etc/ssh/ssh_config
    # 添加
    ServerAliveInterval 30   # 每30秒向服务器发起心跳
    ServerAliveCountMax 6 # 允许6次无响应
    

或者修改服务器端

    # 打开
    sudo vim /etc/ssh/sshd_config
    # 添加
    ClientAliveInterval 30
    ClientAliveCountMax 6
    

# 流量监控

    # 安装监控软件
    sudo apt-get install vnstat
    

    # 安装好后运行以下命令初始化数据库
    # eth0可以通过ifconfig查看
    sudo vnstat -u -i eth1
    

    # 修改配置文件
    sudo vim /etc/vnstat.conf
    Interface "eth1" #此处修改需要监控的网卡
    

    # 启动守护进程
    sudo /etc/init.d/vnstat start
    # 默认开机启动
    sudo update-rc.d vnstat enable
    

# 查看是否安装iptables

    iptables: /sbin/iptables /usr/share/iptables /usr/share/man/man8/iptables.8.gz #表示已经安装iptables
    apt-get install iptables #如果默认没有安装，请运行此命令安装防火墙
    

编辑iptables配置文件

    sudo vi /etc/iptables.rules
    *filter
    :INPUT DROP [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]
    :syn-flood - [0:0]
    -A INPUT -i lo -j ACCEPT
    -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 20 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 21 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
    -A INPUT -p icmp -m limit --limit 100/sec --limit-burst 100 -j ACCEPT
    -A INPUT -p icmp -m limit --limit 1/s --limit-burst 10 -j ACCEPT
    -A INPUT -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -j syn-flood
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    -A syn-flood -p tcp -m limit --limit 3/sec --limit-burst 6 -j RETURN
    -A syn-flood -j REJECT --reject-with icmp-port-unreachable
    COMMIT
    

    # sudo iptables-restore < /etc/iptables.rules #使防火墙规则生效，重启主机执行此命令
    

    # 重启主机会使以上配置失效，以下内容为如何配置开机自动启动防火墙
    # vi /etc/network/if-pre-up.d/iptables #创建文件，添加以下内容，使防火墙开机启动
    #!/bin/bash
    iptables-restore < /etc/iptables.rules
    

    # chmod +x /etc/network/if-pre-up.d/iptables #添加执行权限
    # sudo iptables -L -n查看规则是否生效.
    

# 挂载新的硬盘

    fdisk -l
    如果出现
    Disk /dev/sdb doesn't contain a valid partition table
    说明没有分区,需要处理
    fdisk /dev/sdb
    Command (m for help):  m为查看选项，这里我们输入n表示创建一个新的分区
    选中默认，在分区大小中填写分区大小 +500G
    

第二步：使新创建的分区在系统中立即生效 root#partprobe 第三步：格式化新创建的分区

    mkfs.ext3 /dev/hdb1
    

第四步：使新创建的分区挂载/mnt/ssd_one 目录下

    mount /dev/hda1 /mnt/rhce01
    

查看已经挂载的信息

    sudo vim /etc/mtab
    

第五步：开机自动挂载

    sudo vim /etc/fstab
    # 底部添加
    /dev/sdb1              /mnt/hdd_one              ext3    defaults        0 0
    

查看现在的硬盘信息

    root@m1095:~# df -hl
    Filesystem      Size  Used Avail Use% Mounted on
    udev             12G  4.0K   12G   1% /dev
    tmpfs           2.4G  632K  2.4G   1% /run
    /dev/sda2        71G  1.2G   66G   2% /
    none            4.0K     0  4.0K   0% /sys/fs/cgroup
    none            5.0M     0  5.0M   0% /run/lock
    none             12G     0   12G   0% /run/shm
    none            100M     0  100M   0% /run/user
    /dev/sda1       226M   35M  175M  17% /boot
    /dev/sdb1       493G   70M  467G   1% /mnt/hdd_one
    

# ftp服务器搭建

    sudo apt-get install vsftpd
    

编辑配置文件

    sudo vim /etc/vsftpd.conf
    

    anonymous_enable=NO   # 禁止匿名用户
    local_enable=YES   # 允许本地用户
    write_enable=YES # 允许写入
    chroot_local_user=YES  # 登陆用户被限制在自己的home目录下
    allow_writeable_chroot=YES # 配合chroot_local_user=YES使用
    # 当chroot_local_user=YES表示chroot_list为可以访问其他目录的用户
    # 当chroot_local_user=NO表示chroot_list为不可以访问其他目录的用户，
    # chroot_list_enable=YES  
    # chroot_list_file=/etc/vsftpd.chroot_list
    
    # 以下内容为添加内容
    # 设置为YES表示开启用户登陆过滤，设置为NO表示不开启用户过滤，所有用户都可以访问
    userlist_enable=YES
    # 在userlist_enable=YES的前提下，设置是否阻扯user_list文件中的用户登录FTP服务器
    # YES表示user_list为黑名单，在文件中的用户不能访问，不在文件中的用户可以访问
    # NO表示user_list 为白名单，在文件中的用户可以访问，不在文件中的用户不能访问
    userlist_deny=YES
    

    sudo vim /etc/vsftpd.user_list
    添加root到文件中
    

出现500 OOPS: vsftpd: refusing to run with writable root inside chroot()错误的时候 原因：chroot设置为yes的时候，需要取消ftp根目录的写权限

    # 在配置文件中添加此设置
    allow_writeable_chroot=YES 
    

# 开启防火墙后vsftpd连接错误处理

载入以下2个模块

    sudo modprobe ip_nat_ftp
    sudo modprobe ip_conntrack_ftp
    

    sudo vi /etc/modules
    
    # Add these lines:
    ip_nat_ftp
    ip_conntrack_ftp
    

重启vsftpd服务

# 安装lamp服务

安装apache

    sudo apt-get install apache2
    # 将用户添加到apache用户组中
    sudo usermod -a -G www-data username # www-data 为apache用户组名，username为登陆用户名
    # apache2安装rewrite
    sudo a2enmod rewrite
    sudo service apache2 restart
    # 删除显示的版本信息
    ServerTokens Prod
    ServerSignature Off 
    

配置虚拟主机

    sudo vim /etc/apache.2/apache2.conf
    # 添加以下内容
    <Directory /home/zhc/www/>
            Options FollowSymLinks　#　不要加Index
            AllowOverride All
            Require all granted
    </Directory>
    

安装mysql

    sudo apt-get install mysql-server php5-mysql
    ＃配置mysql
    sudo mysql_secure_installation
    

查看mysql是否开启慢查询日志

    mysql> show variables like '%slow%';
    +---------------------------+---------------------------------------+
    | Variable_name             | Value                                 |
    +---------------------------+---------------------------------------+
    | log_slow_admin_statements | OFF                                   |
    | log_slow_slave_statements | OFF                                   |
    | slow_launch_time          | 2                                     |
    | slow_query_log            | OFF                                   |
    | slow_query_log_file       | /var/lib/mysql/NC-PH-1551-49-slow.log |
    +---------------------------+---------------------------------------+
    5 rows in set (0.00 sec)
    

    #开启慢查询
    mysql> set global log_slow_queries  = 'ON';
    mysql> show variables like '%slow%';
    

    #开启常规查询
    vim /etc/myql/mysql.conf.d/mysqld.conf
    general_log_file        = /var/log/mysql/mysql.log
    general_log             = 1
    #开启错误查询
    log_error = /var/log/mysql/error.log
    #　防止innodb ibdata1文件过大
    innodb_file_per_table=1　
    

安装php

    sudo apt-get install php5 libapache2-mod-php5 php5-mcrypt
    #ubuntu16.04安装方法
    # Add the PPA
    apt-get install software-properties-common #如果提示没有 add-apt-repository，需要执行此命令安装
    sudo add-apt-repository ppa:ondrej/php
    # If you get add-apt-repository: command not found run the following command first :
    sudo apt-get install software-properties-common
    sudo apt-get update
    sudo apt-get install php5.6
    sudo apt-get install php5.6-*
    

配置php

    vim /etc/php/5.6/apache/php.ini
    expose_php = Off # 隐藏php版本信息
    

# 静态文件同步

定时任务 crontab -e # 编辑当前用户的定时任务 crontab -u hanchang -e # 编辑指定用户的定时任务

    # m h  dom mon dow   command
    # 分钟 小时 日期 月份 星期 命令
    0 0 * * * /bin/sh /home/hanchang/www/scripts/rsyncHK.sh