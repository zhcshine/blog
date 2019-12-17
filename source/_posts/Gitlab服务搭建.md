---
title: Gitlab服务搭建
categories:
- 运维
date: 2017-06-19 14:18:48
tags:
- Github
- Gitlab
---

## 安装
```shell
# 安装相关服务 postfix为邮件服务
$ sudo apt-get install curl openssh-server ca-certificates postfix
# 安装gitlab服务包
$ curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
$ sudo apt-get install gitlab-ce
```

# 配置
```shell
# 配置服务
$ sudo vim /etc/gitlab/gitlab.rb
```
```text
## GitLab URL
##! URL on which GitLab will be reachable.
##! For more details on configuring external_url see:
##! https://docs.gitlab.com/omnibus/settings/configuration.html#configuring-the-external-url-for-gitlab
external_url 'http:/192.168.0.19:1500'    # 地址

.
.
.
# 邮件基本设置
### Email Settings
gitlab_rails['gitlab_email_enabled'] = true
gitlab_rails['gitlab_email_from'] = 'admin@uscnk.com'
gitlab_rails['gitlab_email_display_name'] = 'Gitlab'
gitlab_rails['gitlab_email_reply_to'] = 'admin@uscnk.com'
gitlab_rails['gitlab_email_subject_suffix'] = 'Gitlab'

.
.
.
### GitLab email server settings
###! Docs: https://docs.gitlab.com/omnibus/settings/smtp.html
###! **Use smtp instead of sendmail/postfix.**

# gitlab_rails['smtp_enable'] = true
# gitlab_rails['smtp_address'] = "smtp.server"
# gitlab_rails['smtp_port'] = 465
# gitlab_rails['smtp_user_name'] = "smtp user"
# gitlab_rails['smtp_password'] = "smtp password"
# gitlab_rails['smtp_domain'] = "example.com"
# gitlab_rails['smtp_authentication'] = "login"
# gitlab_rails['smtp_enable_starttls_auto'] = true
# gitlab_rails['smtp_tls'] = false

# 邮件服务地址
# QQ exmail (腾讯企业邮箱)
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "admin@uscnk.com"
gitlab_rails['smtp_password'] = "xxxxxxxxx"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = true
gitlab_rails['gitlab_email_from'] = 'admin@uscnk.com'


# nginx基本设置
################################################################################
## GitLab Nginx
##! Docs: https://docs.gitlab.com/omnibus/settings/nginx.html
################################################################################

nginx['listen_addresses'] = ['192.168.0.19']
nginx['losten_port'] = 1500

```

## 启动
```shell
# 重启服务
sudo gitlab-ctl reconfigure
# 重启邮件服务
sudo /etc/init.d/postfix restart
# 访问
http://192.168.0.19:1500
Username: root
Password: 5iveL!fe

```
## github与gitlab共存
```shell
# github账户邮箱， 将id_rsa改名成id_rsa_github
$ ssh-keygen -t rsa -C "xxxxx@gmail.com"
# gitlab公司邮箱， 将id_rsa改名成id_rsa_gitlab
$ ssh-keygen -t rsa -C "xxxxxx@work.com"
# 添加私钥
$ ssh-add ~/.ssh/id_rsa_github
$ ssh-add ~/.ssh/id_rsa_gitlab

#在 ~/.ssh 目录下新建一个config文件
$ touch config
$ chmod 755 conifg

```
```text
# 并添加以下内容
# gitlab
Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_github
# github
Host gitlib.com
    HostName gitlib.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_gitlab

```
```shell
# 将id_rsa_github.pab内容添加到github中
# 测试结果
$ ssh -T git@github.com
# 将id_rsa_gitlab.pab内容添加到gitlab中
$ cd ~/mytest
# 在github中测试
$ git remote add github https://github.com/zhcshine/mytest.git
$ git push -u github master
# 在gitlab中测试
$ git remote add gitlab http://192.168.0.19:1500/zhcshine/mytest.git
$ git push -u gitlab master
```