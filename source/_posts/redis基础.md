---
title: redis基础
tags:
  - redis
  - 数据库
url: 486.html
id: 486
comments: false
categories:
  - 后端
  - 运维
date: 2018-01-11 15:51:36
---

REmote DIctionary Server(Redis) 是一个由Salvatore Sanfilippo写的key-value存储系统。 Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。 它通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Map), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。

### redis安装

    $sudo apt-get update
    $sudo apt-get install redis-server
    $redis-cli
    

### redis配置

    sudo vim /etc/redis.conf
    # 设置为守护进程
    protected-mode yes
    # 设置密码
    requirepass redispassword
    # port
    port 6379
    # bind 127.0.0.1
    bing 0.0.0.0 # 允许远程访问
    
    

### 客户端远程登录

    redis-cli -h 127.0.0.1 -p 6379 -a redispassword
    

### redis客户端

    # redis-commander，基于nodejs
    $ npm install -g redis-commander
    $ redis-commander
    

### 通用命令

    $ 127.0.0.1:6379> del name # 删除数据
    $ 127.0.0.1:6379> type name # 查看数据类型
    $ 127.0.0.1:6379> rename name # 重命名
    
    

### 数据结构

字符串string

    $ 127.0.0.1:6379>  set string-name value
    $ 127.0.0.1:6379> get string-name
    $ 127.0.0.1:6379>  del string-name
    

列表list： 相当于默认键的数组

    $ 127.0.0.1:6379> lpush list-name value1 value2 value3 ... # 左侧插入列表值
    (integer) 3
    $ 127.0.0.1:6379> rpush list-name value4 value5 value6... # 右侧插入列表值
    (integer) 6
    $ 127.0.0.1:6379>  lindex list-name 1 # 获取第1个的值
    "value2"
    $ 127.0.0.1:6379>  lrange list-name 1 2 # 获取从第1位置到2位置的列表值
    1) "value2"
    2) "value1"
    $ 127.0.0.1:6379>  lpop list-name # 弹出最左边值
    "value3"
    $ 127.0.0.1:6379> rpop list-name # 弹出最右边值
    "value6"
    

集合set：无序，不重复的列表

    $ 127.0.0.1:6379>  sadd set-name value1 value2 value3 # 设置无序列表值
    (integer) 3
    $ 127.0.0.1:6379> smembers set-name  # 查看所有值
    1) "value3"
    2) "value2"
    3) "value1"
    $ 127.0.0.1:6379> srem set-name value1 # 删除值
    (integer) 1
    $ 127.0.0.1:6379> sismember set-name value2 # 查看是否在无序列表内
    (integer) 1
    

散列值hash：相当于自定义键的数组

    $ 127.0.0.1:6379> HSET hash-name key1 value1 #设置键值对
    (integer) 1
    $ 127.0.0.1:6379> HGET hash-name key1 # 获取指定键的值
    "value1"
    $ 127.0.0.1:6379> HSET hash-name key1 value2 # 更新键的值
    (integer) 0
    $ 127.0.0.1:6379> HGET hash-name key1 
    "value2"
    $ 127.0.0.1:6379> HSET hash-name key2 value2
    (integer) 1
    $ 127.0.0.1:6379> HGETALL hash-name # 查找所有键值对
    1) "key1"
    2) "value2"
    3) "key2"
    4) "value2"
    $ 127.0.0.1:6379> HDEL hash-name key1 # 删除hash键值对
    (integer) 1
    

有序集合zset：成员+分值，分值必须是浮点数，通过浮点数进行排序

    $ 127.0.0.1:6379> zadd zset-name 100 member1 # 添加键值对
    (integer) 1
    $ 127.0.0.1:6379> zadd zset-name 202 member2
    (integer) 1
    $ 127.0.0.1:6379> zadd zset-name 803 member3
    (integer) 1
    $ 127.0.0.1:6379> ZRANGE zset-name 0 -1 # 获取多个元素
    1) "member1"
    2) "member2"
    3) "member3"
    $ 127.0.0.1:6379> ZRANGE zset-name 0 -1 withscores # 获取多个元素
    1) "member1"
    2) "100"
    3) "member2"
    4) "202"
    5) "member3"
    6) "803"
    $ 127.0.0.1:6379> ZRANGEBYSCORE zset-name 0 300 withscores # 按score获取多个元素
    1) "member1"
    2) "100"
    3) "member2"
    4) "202"
    $ 127.0.0.1:6379> ZREM zset-name member1 # 移除一个元素
    (integer) 1