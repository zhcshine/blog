---
title: centos7.2安装使用sentry
url: 553.html
id: 553
comments: false
categories:
  - 运维
date: 2018-09-08 14:48:41
tags:
---

    # 先安装docker  https://docs.docker.com/install/linux/docker-ce/centos/#install-docker-ce
    # 后安装sentry   https://github.com/getsentry/onpremise
    $ mkdir sentry
    $ cd sentry
    $ mkdir -p data/{sentry,postgres}
    $ git clone https://github.com/getsentry/onpremise
    $ cd onpremise
    $ docker-compose run --rm web config generate-secret-key # 创建一个secret key并配置到docker-compose.yml中的SENTRY_SECRET_KEY.
    $ docker-compose run --rm web upgrade # 创建数据库
    $ docker-compose up -d  # 后台执行