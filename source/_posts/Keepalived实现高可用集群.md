---
title: Keepalived实现高可用集群
categories:
  - 运维
date: 2017-04-19 14:30:29
tags:
---

### CENTOS6.8 192.168.0.106 MASTER

    # 安装
    sudo yum install keepalived
    # 配置
    sudo vim /etc/keepalived/keepalived.conf
    
    ! Configuration File for keepalived
    
    global_defs {
        # 这些信息是配置报警发邮件信息
       notification_email {
         acassen@firewall.loc
         failover@firewall.loc
         sysadmin@firewall.loc
       }
       notification_email_from Alexandre.Cassen@firewall.loc
       smtp_server 192.168.200.1
       smtp_connect_timeout 30
         # 路由标识，同一局域网应该唯一
       router_id lb01
    }
    vrrp_instance VI_1 {
        # 当前角色是master
        state MASTER
            # 对外提供网络的接口
        interface eth0
            # 虚拟路由id标识，数字，必须和backup里相同实例一致
        virtual_router_id 55
            # 优先级，数字必须比backup大
        priority 150
        advert_int 1
            # 权限认证，和backup一致
        authentication {
            auth_type PASS
            auth_pass 1111
        }
        virtual_ipaddress {
           192.168.0.200
        }
    }
    
    # 重启
    sudo /etc/init.d/keepalived restart
    # 查看结果
    ip addr | grep 192.168.0.200
    

### CENTOS7.2 192.168.0.209 BACKUP

    # 安装
    sudo yum install keepalived
    # 配置
    sudo vim /etc/keepalived/keepalived.conf
    
    ! Configuration File for keepalived
    
    global_defs {
       notification_email {
         acassen@firewall.loc
         failover@firewall.loc
         sysadmin@firewall.loc
       }
       notification_email_from Alexandre.Cassen@firewall.loc
       smtp_server 192.168.200.1
       smtp_connect_timeout 30
       router_id lb02  # 和master不一致
    }
    
    vrrp_instance VI_1 {
        state BACKUP      # 和MASTER不一致
        interface enp0s3
        virtual_router_id 55
        priority 100      # 和master不一致
        advert_int 1
        authentication {
            auth_type PASS
            auth_pass 1111
        }
        virtual_ipaddress {
            192.168.0.200
        }
    }
    # 重启
    sudo systemctl restart keepalived
    # 查看结果
    ip addr | grep 192.168.0.200
    

### CENTOS6.8 192.168.0.106 nginx配置

    # nginx配置文件
        server {
            listen       192.168.0.200:80; # 这里需要指定虚拟vip监听
            server_name  localhost;
    
            location / {
                      # 只访问web站点
               proxy_pass http://www_server_pools;
            }
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
        }
    

### CENTOS7.2 192.168.0.209 配置nginx

    # nginx配置文件
       server {
            listen       192.168.0.200:80;
            server_name  localhost;
    
            location / {
                         # 只访问phone站点
               proxy_pass http://www_phone_pools;
            }
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
        }
    
    

### 测试结果

    # 开启192.168.0.106，192.168.0.209的keepalive服务
    
    # 查看192.168.0.106
    $ ip addr | grep 192.168.0.200
    inet 192.168.0.200/32 scope global eth0 # 应该有这行结果
    
    # 查看192.168.0.209
    $ ip addr | grep 192.168.0.200 # 结果应该为空，如果有结果，表示脑裂，检查相关配置和防火墙
    
    # 访问http://192.168.200 
    curl 192.168.0.200 # 结果应该只访问web网站
    
    
    # 关停192.168.0.106的keepalive服务
    
    # 访问http://192.168.200 
    curl 192.168.0.200 # 结果应该只访问phone网站
    
    # 查看192.168.0.106
    $ ip addr | grep 192.168.0.200
    inet 192.168.0.200/32 scope global eth0 # 结果应该为空
    
    # 查看192.168.0.209
    $ ip addr | grep 192.168.0.200 
    inet 192.168.0.200/32 scope global enp0s3 # 此时有这行结果
    
    # 重启192.168.0.106的keepalive服务，应该恢复之前的测试结果