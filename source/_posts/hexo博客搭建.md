---
title: hexo博客搭建
date: 2019-08-19 08:57:45
tags:
---

#### 安装基础环境

{% post_link node入门基础 %}

#### 安装hexo

```shell
npm install -g hexo-cli 
```

#### hexo部署配置

```shell
sudo vim _config.yml
```
```shell
deploy:
  type: git
  repo: https://github.com/zhcshine/zhcshine.github.io.git  # 在github上新建一个page项目
  branch: master
```

#### hexo命令

```shell
hexo init blog  # 初始化博客
hexo n test_my_site  # 创建一篇博客文章
hexo g  # 打包
hexo s  # 预览 http://localhost:4000
hexo d  # 发布到线上
```