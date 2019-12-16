---
title: Anaconda入门基础
date: 2019-11-06 13:38:28
tags:
- python
categories:
- 后端
---

## 安装

### 官方安装

[官方下载地址](https://www.anaconda.com/distribution/)

### archlinux安装
```
➜ sudo pacman -S anaconda
```

**验证**
```shell
➜ conda --version
conda 4.7.10
```

## 虚拟环境

### 创建
```shell
# conda create --name <env_name> <package_names> <package_names> ....
➜ conda create --name ml python=3.7.3 numpy pandas
```

### 进入/退出
```shell
➜ conda activate ml
➜ conda deactivate
```

### 列表
```shell
(ml)$ conda info --envs
# conda environments:
#
ml                    *  /home/zhc/.conda/envs/ml
base                     /opt/anaconda
# 或者
(ml)$ conda env list
# conda environments:
#
ml                    *  /home/zhc/.conda/envs/ml
base                     /opt/anaconda
```

### 删除
```shell
conda remove --name <env_name> --all
```

## 包管理

### 当期环境包列表
```shell
(ml)$ conda list
```

### 当期环境安装包
```shell
# (ml)$ conda install <package_name>
(ml)$ conda install jupyter notebook  # 安装jupyter notebook编辑器
$ jupter notebook  # 打开编辑器
```

### 卸载包
```shell
(ml)$ conda remove <package_name>
```