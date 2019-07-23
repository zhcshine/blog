---
title: Rsync同步web服务器
tags:
  - windows
url: 39.html
id: 39
comments: false
categories:
  - 运维
date: 2016-11-09 11:05:34
---

### 测试环境

服务端安装 WIN2008R2 ip:192.168.0.160  
客户端安装 WIN2003 ip:192.168.0.206

### 程序下载

新版已经开始收费，这里使用旧版4.0.5,百度网盘下载http://pan.baidu.com/s/1kTNyk1h

### 服务端安装

![](http://7xq3ry.com1.z0.glb.clouddn.com/cwrsync.png) 一路直接下一步，这里会创建一个用户，创建的默认密码可以不需要修改，直接下一步。 ![](http://7xq3ry.com1.z0.glb.clouddn.com/cwrsync1.png) 创建的用户 ![](http://7xq3ry.com1.z0.glb.clouddn.com/cwrsync2.png) 创建的服务改为自动启动

### 客户端安装

一路下一步直到完成。

### 配置服务端

    use chroot = false
    # 不验证用户密码
    strict modes = false
    lock file = rsyncd.lock 
    hosts allow = 192.168.0.206
    max connections = 5
    # 默认端口
    port = 873
    
    # 指定匿名访问
    uid = 0
    gid = 0
    
    
    log file = rsyncd.log
    
    # Module definitions
    # Remember cygwin naming conventions : c:\work becomes /cygdrive/c/work
    
    [web]
    path = /cygdrive/C/WEB
    # 下面两行是配置用户名密码的
    # auth users = dbbackuper
    # secrets file = /cygdrive/e/Setting/Rsync/rsync_db.ps
    # 只允许客户端下载，不允许客户端上传
    read only = true
    transfer logging = yes
    

防火墙设置允许配置的端口通过  
查看端口配置是否错误

    telnet 192.168.0.160 873  
    @RSYNCD: 30.0
    

服务器端配置测试文件目录 ![](http://7xq3ry.com1.z0.glb.clouddn.com/cwrsync3.png) 其中targetDir为联接文件

    C:\>mklink /J WEB\targetDir\ source\
    为 WEB\targetDir\ <<===>> source\ 创建的联接
    

### 配置客户端

在安装目录下使用如下命令

    C:\Program Files\cwRsync\bin>rsync --port=873 -vzrtopg --progress --delete 192.168.0.160::web /cygdrive/C/WEB
    receiving incremental file list
    skipping non-regular file "targetDir"
    ./
    TEST4/
    test3/
    
    sent 39 bytes  received 163 bytes  134.67 bytes/sec
    total size is 19  speedup is 0.09
    

查看客户端文件目录

    test3
    TEST4
    

提示skipping non-regular file 'targetDir'忽略软链接解决办法  
参数-L:--copy-links 像对待常规文件一样处理软链结

    C:\Program Files\cwRsync\bin>rsync --port=873 -vzrtopgL --progress --delete 192.
    168.0.160::web /cygdrive/C/WEB
    receiving incremental file list
    deleting test5/adda.txt
    deleting test5/
    ./
    TEST4/
    targetDir/
    targetDir/HAFA/
    targetDir/pdf/
    targetDir/pdf/TARGE.txt
               0 100%    0.00kB/s    0:00:00 (xfer#1, to-check=1/8)
    test3/
    
    sent 70 bytes  received 272 bytes  97.71 bytes/sec
    total size is 0  speedup is 0.00
    

### 实现客户端定时任务

1，要添加环境变量到系统中  
2，上传脚本文件到客户端sync_client.bat

    rsync --port=873 -vzrtopgL --progress --delete 192.168.0.160::web /cygdrive/C/WEB
    

3,在系统中添加定时任务，并指定更新时间

### 具体参数详解

rsync参数的具体解释如下： -v, --verbose 详细模式输出  
-q, --quiet 精简输出模式  
-c, --checksum 打开校验开关，强制对文件传输进行校验  
-a, --archive 归档模式，表示以递归方式传输文件，并保持所有文件属性，等于-rlptgoD  
-r, --recursive 对子目录以递归模式处理  
-R, --relative 使用相对路径信息  
-b, --backup 创建备份，也就是对于目的已经存在有同样的文件名时，将老的文件重新命名为~filename。可以使用--suffix选项来指定不同的备份文件前缀。  
--backup-dir 将备份文件(如~filename)存放在在目录下。  
-suffix=SUFFIX 定义备份文件前缀  
-u, --update 仅仅进行更新，也就是跳过所有已经存在于DST，并且文件时间晚于要备份的文件。(不覆盖更新的文件)  
-l, --links 保留软链结  
-L, --copy-links 想对待常规文件一样处理软链结  
--copy-unsafe-links 仅仅拷贝指向SRC路径目录树以外的链结  
--safe-links 忽略指向SRC路径目录树以外的链结  
-H, --hard-links 保留硬链结  
-p, --perms 保持文件权限  
-o, --owner 保持文件属主信息  
-g, --group 保持文件属组信息  
-D, --devices 保持设备文件信息  
-t, --times 保持文件时间信息  
-S, --sparse 对稀疏文件进行特殊处理以节省DST的空间  
-n, --dry-run现实哪些文件将被传输  
-W, --whole-file 拷贝文件，不进行增量检测  
-x, --one-file-system 不要跨越文件系统边界  
-B, --block-size=SIZE 检验算法使用的块尺寸，默认是700字节  
-e, --rsh=COMMAND 指定使用rsh、ssh方式进行数据同步  
--rsync-path=PATH 指定远程服务器上的rsync命令所在路径信息  
-C, --cvs-exclude 使用和CVS一样的方法自动忽略文件，用来排除那些不希望传输的文件  
--existing 仅仅更新那些已经存在于DST的文件，而不备份那些新创建的文件  
--delete 删除那些DST中SRC没有的文件  
--delete-excluded 同样删除接收端那些被该选项指定排除的文件  
--delete-after 传输结束以后再删除  
--ignore-errors 及时出现IO错误也进行删除  
--max-delete=NUM 最多删除NUM个文件  
--partial 保留那些因故没有完全传输的文件，以是加快随后的再次传输  
--force 强制删除目录，即使不为空  
--numeric-ids 不将数字的用户和组ID匹配为用户名和组名  
--timeout=TIME IP超时时间，单位为秒  
-I, --ignore-times 不跳过那些有同样的时间和长度的文件  
--size-only 当决定是否要备份文件时，仅仅察看文件大小而不考虑文件时间  
--modify-window=NUM 决定文件是否时间相同时使用的时间戳窗口，默认为0  
-T --temp-dir=DIR 在DIR中创建临时文件  
--compare-dest=DIR 同样比较DIR中的文件来决定是否需要备份  
-P 等同于 --partial  
--progress 显示备份过程  
-z, --compress 对备份的文件在传输时进行压缩处理  
--exclude=PATTERN 指定排除不需要传输的文件模式  
--include=PATTERN 指定不排除而需要传输的文件模式  
--exclude-from=FILE 排除FILE中指定模式的文件  
--include-from=FILE 不排除FILE指定模式匹配的文件  
--version 打印版本信息  
--address 绑定到特定的地址  
--config=FILE 指定其他的配置文件，不使用默认的rsyncd.conf文件  
--port=PORT 指定其他的rsync服务端口  
--blocking-io 对远程shell使用阻塞IO  
-stats 给出某些文件的传输状态  
--progress 在传输时现实传输过程  
--log-format=formAT 指定日志文件格式  
--password-file=FILE 从FILE中得到密码  
--bwlimit=KBPS 限制I/O带宽，KBytes per second  
-h, --help 显示帮助信息

### 部分错误的解决办法

1.'rsync' 不是内部或外部命令，也不是可运行的程序或批处理文件。  
加环境变量 path = C:\\Program Files (x86)\\cwRsync\\bin 2.rsync: failed to connect to 192.168.1.82: Connection timed out (116) rsync error: error in socket IO (code 10) at clientserver.c(122) \[Receiver=3.0.7\]  
网络通畅，服务器端允许访问端口 873 3.@ERROR: invalid uid nobody  
rsync error: error starting client-server protocol (code 5) at main.c(1506) \[Receiver=3.0.7\]  
指定uid gid  
uid = 0  
gid = 0 4.@ERROR: chdir failed  
rsync error: error starting client-server protocol (code 5) at main.c(1506) \[Receiver=3.0.7\]  
path目录配置的正确，得存在  
解决：服务器端同步目录没有权限，cwrsync默认用户是Svcwrsync。为同步目录添加用户Svcwrsync权限。  
也可以通过 菜单--cwRsyncServer--02. Prep a Dir for Upload 配置目录权限 除完全控制和特殊权限外的所有权限 5.@ERROR: auth failed on module test  
rsync error: error starting client-server protocol (code 5) at main.c(1506) \[Receiver=3.0.7\]  
客户端设置  
a.在命令上要指定好用户名  
b.密码文件只写密码  
rsyncd.secrets文件  
rsyncpass  
c.用户名密码都要正确 6.Unexpected local arg: /cygdrive/d/rsyncBackup  
If arg is a remote file/dir, prefix it with a colon (:).  
rsync error: syntax or usage error (code 1) at main.c(1218) \[Receiver=3.0.7\]  
不一定是这个路径有问题，可能是--password-file路径中有空格，服务器端没问题，客户端好像不行 7.password file must be owned by root when running as root  
continuing without password file  
Password:  
设置密码访问权限chown.exe可从服务器端拷贝过来  
chmod -c 600 /cygdrive/c/etc/rsyncd.secrets  
chown administrator /cygdrive/c/etc/rsyncd.secrets  
服务器端不设也可以 8.当出现rsync: chown “” failed: Invalid argument (22)错误时，是因为参数中有o，即保留文件的owner信息，而window服务端无法完成chown操作，解决办法是去掉参数o。另外有些可能还会出现rsync: chgrp “” failed: Invalid argument (22)，原因类似，只要去掉参数中的g即可。