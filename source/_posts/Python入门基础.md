---
title: python入门基础
tags:
- Python
categories:
- 后端
date: 2016-11-09 10:46:24
---

## 安装

### linux源码安装（适合生产环境）
安装python3.x.x
```shell
# 以centos系统安装python3.6.6为例
$ sudo yum groupinstall "Development tools"
$ sudo yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
$ wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tar.xz
$ tar -xJf Python-3.6.6.tar.xz
$ cd Python-3.6.6
$ ./configure --prefix=/usr/local/python3  # 编译配置
$ sudo make
$ sudo make install
# 创建软链接
$ ln -s /usr/local/python3/bin/python3 /usr/bin/python3
$ ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```

安装虚拟环境
```shell
sudo yum install python-virtualenv  # centos
```

创建项目
```shell
$ virtualenv --no-site-packages -p /usr/bin/python3 .env-project
$ source.env-project/bin/active  # 进入虚拟环境
(.env-project) deactive  # 退出虚拟环境
```

### pyenv安装(适合开发环境)

安装pyenv
```shell
curl -L https://raw.githubusercontent.com/pyenv/pyenv-installer/master/bin/pyenv-installer | bash
```

设置环境变量
```shell
$ vim ~/.zshrc  # 具体文件看自己的终端工具
```

```text
export PATH=$HOME/.pyenv/bin:$PATH
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

```shell
$ source ~/.zshrc
```

检查环境

```shell
$ pyenv doctor  # 如有错误，根据自己的环境解决
```

安装python3.x.x

```shell
$ pyenv install 3.6.6
```

如果pyenv下载文件缓慢，可以先下载文件到缓存中

```shell
$ cd ~/.pyenv/cache  # 没有则创建文件
$ wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tar.xz
$ pyenv install 3.6.6
```

依次安装python3.7.3 python2.7.10

查看当前系统所有python版本

```shell
$ pyenv versions
* system (set by /home/zhc/.pyenv/version)
  2.7.10
  3.6.6
  3.7.3
```

pyenv系统级别global切换版本<pyenv目录级别local切换版本<pyenv当前shell级别切换版本

```shell
$ pyenv global 3.6.6                                                                 
$ pyenv version                                                                     
3.6.6 (set by /home/zhc/.pyenv/version)                                                 
$ pyenv global 3.7.3
$ pyenv version
3.7.3 (set by /home/zhc/.pyenv/version)
$ pyenv shell 3.6.6
$ pyenv version
3.6.6 (set by PYENV_VERSION environment variable) 
$ pyenv local 3.6.6  # 会在当前文件夹下创建.python-version文件，存储虚拟环境名称 
```

pyenv安装虚拟环境

```shell
$ pyenv virtualenv 3.6.6 test-3.6.6
$ pyenv virtualenv 3.7.3 test-3.7.3
```

pyenv删除虚拟环境

```shell
$ pyenv uninstall test-3.6.6
```

pyenv查看全部虚拟环境
```shell
$ pyenv virtualenvs                                                                 
3.6.6/envs/test-3.6.6 (created from /home/zhc/.pyenv/versions/3.6.6)                 
3.7.3/envs/test-3.7.3 (created from /home/zhc/.pyenv/versions/3.7.3)                 
test-3.6.6 (created from /home/zhc/.pyenv/versions/3.6.6)
test-3.7.3 (created from /home/zhc/.pyenv/versions/3.7.3)
```

切换虚拟环境

```shell
$ pyenv activate test-3.6.6
$ pyenv local test-3.6.6
$ source deactivate
```

pyenv重建环境变量

```shell
$ pyenv rehash
```


## 特殊方法

### 获取文件修改时间
```python
import time
filemt= time.localtime(os.stat(filename).st_mtime)  #文件修改时间
filect = time.localtime(os.stat(filename).st_ctime)  #文件创建
print time.strftime("%Y-%m-%d",filemt)  
print time.strftime("%Y-%m-%d",filect)
```

### 获取多少天以前的时间
```python
import datetime
# 30天以前的日期
date_time = (datetime.datetime.now() - datetime.timedelta(days=30)).strftime("%Y-%m-%d")
print date_time
```


### 去除列表重复项内容
```python
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
```

    

### md5，hash加密
```python
import hashlib
data =  'This a md5 test!'
hash_md5 = hashlib.md5(data)  # md5
hash_md5.hexdigest()
hashlib.sha1('This is a sha1 test!').hexdigest() # sha1
```

### 打印输出对象
```python
from pprint import pprint
pprint(vars(object))
```
