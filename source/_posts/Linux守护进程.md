---
title: linux守护进程
date: 2019-07-26 13:43:45
tags:
  - linux
  - 运维
---

# 什么是守护进程？

[守护进程（Daemon Process）](https://zh.wikipedia.org/wiki/%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8B)，也就是通常说的 Daemon 进程（精灵进程），是 Linux 中的后台服务进程。它是一个生存期较长的进程，通常独立于控制终端并且周期性地执行某种任务或等待处理某些发生的事件。

守护进程是个特殊的孤儿进程，这种进程脱离终端，为什么要脱离终端呢？之所以脱离于终端是为了避免进程被任何终端所产生的信息所打断，其在执行过程中的信息也不在任何终端上显示。由于在 Linux 中，每一个系统与用户进行交流的界面称为终端，每一个从此终端开始运行的进程都会依附于这个终端，这个终端就称为这些进程的控制终端，当控制终端被关闭时，相应的进程都会自动关闭。

Linux 的大多数服务器就是用守护进程实现的。

# 守护进程的分类

根据守护进程的启动和管理方式，可以分为独立启动守护进程和超级守护进程两类

**独立启动（stand_alone)**:该类进程启动后就常驻内存，所以会一直占用系统资源。其最大的优点就是它会一直启动，当外界有要求时相应速度较快，像httpd等进程；
   
**超级守护进程**：系统启动时由一个统一的守护进程xinet来负责管理一些进程，当相应请求到来时需要通过xinet的转接才可以唤醒被xinet管理的进程。这种进程的优点时最初只有xinet这一守护进程占有系统资源，其他的内部服务并不一直占有系统资源，只有数据包到来时才会被xinet管理员来唤醒。并且我们还可以通过xinet来对它所管理的进程设置一些访问权限，相当于多了一层管理机制。
如果用两个比喻来形容两类守护进程的话一般会用银行的业务处理窗口来类比：
   
**独立启动**：银行里有一种单服务的窗口，像取钱，存钱等窗口，这些窗口边上始终会坐着一个人，如果有人来取钱或存钱，可以直接到相应的窗口去办理，这个处理单一服务的始终存在的人就是独立启动的守护进程；

**超级守护进程**：银行里还有一种窗口，提供综合服务，像汇款，转账，提款等业务；这种窗口附近也始终坐着一个人（xinet），她可能不提供具体的服务，提供具体服务的人在里面闲着聊天啊，喝茶啊，但是当有人来汇款时他会大声喊一句，小王，有人汇款啦，然后里面管汇款的小王会立马跑过来帮忙办完汇款业务。其他的人继续聊天，喝茶。这些负责具体业务的人我们就称之为超级守护进程。当然可能汇款人会有一些规则，可能不能往北京汇款，他就会提早告诉xinet，所以如果有人来汇款想汇往北京的话，管理员就直接告诉他这个我们这里办不到的，于是就根本不会去喊汇款员了，相当于提供了一层管理机制。针对这种窗口还存在多线程和单线程的区别：多线程：将所有用户的要求都提上来，里面的人都别闲着了，都一起干活吧；

**单线程**：大家都排好队了，一个一个来，里面的人同一时间只有一个人在工作。

当然每个守护进程都会监听一个端口（银行窗口），一些常用守护进程的监听端口是固定的，像httpd监听80端口， sshd监听22端口等；我们可以将其理解为责任制，时候等待，有求必应。具体的端口信息可以通过cat /etc/services来查看。


# 查看守护进程
```shell
ps axj
```
> a 表示不仅列当前用户的进程，也列出所有其他用户的进程

> x 表示不仅列有控制终端的进程，也列出所有无控制终端的进程

> j 表示列出与作业控制相关的信息
```shell
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND                                                                                                                                              
    0     1     1     1 ?           -1 Ss       0  12:00 /sbin/init                                                                                                                                           
    0     2     0     0 ?           -1 S        0   0:00 [kthreadd]                                                                                                                                           
    2     3     0     0 ?           -1 I<       0   0:00 [rcu_gp]                                                                                                                                             
    2     4     0     0 ?           -1 I<       0   0:00 [rcu_par_gp]                                                                                                                                         
    2     6     0     0 ?           -1 I<       0   0:00 [kworker/0:0H-kblockd]                                                                                                                               
    2     8     0     0 ?           -1 I<       0   0:00 [mm_percpu_wq]                                                                                                                                       
    2     9     0     0 ?           -1 S        0   3:28 [ksoftirqd/0]                                                                                                                                        
    2    10     0     0 ?           -1 I        0  40:58 [rcu_preempt]                                                                                                                                        
    2    11     0     0 ?           -1 I        0   0:00 [rcu_sched]                                                                                                                                          
    2    12     0     0 ?           -1 I        0   0:00 [rcu_bh]                                                                                                                                             
    2    13     0     0 ?           -1 S        0   5:16 [rcuc/0]                                                                                                                                             
    2    14     0     0 ?           -1 S        0   0:00 [rcub/0]                                                                                                                                             
```
从上图可以看出守护进行的一些特点：

> 守护进程基本上都是以超级用户启动（ UID 为 0 ）

> 没有控制终端（ TTY 为 ？）

> 终端进程组 ID 为 -1 （ TPGID 表示终端进程组 ID）

一般情况下，守护进程可以通过以下方式启动：

> 在系统启动时由启动脚本启动，这些启动脚本通常放在 /etc/rc.d 目录下；

> 利用 inetd 超级服务器启动，如 telnet 等；

> 由 cron 定时启动以及在终端用 nohup 启动的进程也是守护进程。

# 编写守护进程

**屏蔽一些控制终端操作的信号**

这是为了防止守护进行在没有运行起来前，控制终端受到干扰退出或挂起。

```c
signal(SIGTTOU,SIG_IGN);   
signal(SIGTTIN,SIG_IGN);   
signal(SIGTSTP,SIG_IGN);   
signal(SIGHUP ,SIG_IGN);
```

**在后台运行**

这是为避免挂起控制终端将守护进程放入后台执行。方法是在进程中调用 fork() 使父进程终止， 让守护进行在子进程中后台执行。 

```c
if( pid = fork() ){ // 父进程  
    exit(0);        //结束父进程，子进程继续  
}  
```

**脱离控制终端、登录会话和进程组**

Linux 中的进程与控制终端，登录会话和进程组之间的关系：

进程属于一个进程组，进程组号（GID）就是进程组长的进程号（PID）。登录会话可以包含多个进程组。这些进程组共享一个控制终端。这个控制终端通常是创建进程的 shell 登录终端。 控制终端、登录会话和进程组通常是从父进程继承下来的。我们的目的就是要摆脱它们 ，使之不受它们的影响。因此需要调用 setsid() 使子进程成为新的会话组长，示例代码如下：

```c
setsid();
```

setsid()调用成功后，进程成为新的会话组长和新的进程组长，并与原来的登录会话和进程组脱离。由于会话过程对控制终端的独占性，进程同时与控制终端脱离。 

**禁止进程重新打开控制终端**

现在，进程已经成为无终端的会话组长，但它可以重新申请打开一个控制终端。可以通过使进程不再成为会话组长来禁止进程重新打开控制终端，采用的方法是再次创建一个子进程，示例代码如下：

```c
if( pid=fork() ){ // 父进程  
    exit(0);      // 结束第一子进程，第二子进程继续（第二子进程不再是会话组长）   
}
```

**关闭打开的文件描述符**

进程从创建它的父进程那里继承了打开的文件描述符。如不关闭，将会浪费系统资源，造成进程所在的文件系统无法卸下以及引起无法预料的错误。按如下方法关闭它们：

```c
// NOFILE 为 <sys/param.h> 的宏定义  
// NOFILE 为文件描述符最大个数，不同系统有不同限制  
for(i=0; i< NOFILE; ++i){// 关闭打开的文件描述符  
    close(i);  
}
```

**改变当前工作目录**

进程活动时，其工作目录所在的文件系统不能卸下。一般需要将工作目录改变到根目录。对于需要转储核心，写运行日志的进程将工作目录改变到特定目录如 /tmp。示例代码如下：

```c
chdir("/");  
```

**重设文件创建掩模**

进程从创建它的父进程那里继承了文件创建掩模。它可能修改守护进程所创建的文件的存取权限。为防止这一点，将文件创建掩模清除：

```c
umask(0);
```

**处理 SIGCHLD 信号**

但对于某些进程，特别是服务器进程往往在请求到来时生成子进程处理请求。如果父进程不等待子进程结束，子进程将成为僵尸进程（zombie）从而占用系统资源。如果父进程等待子进程结束，将增加父进程的负担，影响服务器进程的并发性能。在 Linux 下可以简单地将 SIGCHLD 信号的操作设为 SIG_IGN 。

```c
signal(SIGCHLD, SIG_IGN);  
```

这样，内核在子进程结束时不会产生僵尸进程。

代码示例
```c
#include<unistd.h>
#include<signal.h>
#include<fcntl.h>
#include<sys/syslog.h>
#include<sys/param.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<stdio.h>
#include<stdlib.h>
#include<time.h>
#include <string.h>

int init_daemon(void)
{
    int i;

    // 1）屏蔽一些控制终端操作的信号  
    signal(SIGTTOU, SIG_IGN);
    signal(SIGTTIN, SIG_IGN);
    signal(SIGTSTP, SIG_IGN);
    signal(SIGHUP, SIG_IGN);

    // 2）在后台运行 
    pid_t pid = fork();
    if(pid < 0) {
        perror("fork");
        exit(EXIT_FAILURE); // 进程创建失败
    }
    if(pid > 0) {
        exit(0);    // 退出父进程
    }

    // 3）脱离控制终端、登录会话和进程组  
    setsid();

    // 4）禁止进程重新打开控制终端  
    pid = fork();
    if(pid < 0) {
        perror("fork");
        exit(EXIT_FAILURE);
    }
    if (pid > 0) {
        exit(0);  // 结束第一子进程，第二子进程继续（第二子进程不再是会话组长） 
    }

    // 5）关闭打开的文件描述符  
    // NOFILE 为 <sys/param.h> 的宏定义  
    // NOFILE 为文件描述符最大个数，不同系统有不同限制  
    for(i=0; i<NOFILE; ++i){
        close(i);
    }

    // 6）改变当前工作目录  
    chdir("/");

    // 7）重设文件创建掩模  
    umask(0);

    // 8）处理 SIGCHLD 信号  
    signal(SIGCHLD,SIG_IGN);

    return 0;
}



int main(int argc, char *argv[]) {
    init_daemon();
    char *msg = "I'm printlg process...\n";
    int msg_len = strlen(msg);
    int fd = open("/tmp/test_printlg.log", O_RDWR | O_CREAT | O_APPEND, 0666);
    if(fd < 0) {
        printf("open /tmp/test_printlg.log fail.\n");
        exit(EXIT_FAILURE);
    }
    while(1) {
        // 每隔 3s 输出 msg 到 /tmp/test_printlg.log 文件中
        write(fd, msg, msg_len);
        sleep(3);
    }
    close(fd);
    return 0;
}

```

```shell
# 编译
$ gcc -o test1 main.c 
# 运行
$ sudo ./test1
# 查看
$ ps axj | grep test1
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND 
21591 31396 31395 31395 ?           -1 R        0   0:03 ./test1
# 查看结果
$ vim /tmp/test_printlg.log
# 杀死守护进程
$ sudo kill -9 <pid>
```

# 守护进程开机自启动

linux系统启动的顺序
```shell
1. BIOS
2. MBR (boot loader)
3. kernel loading
4. init program
5. initial script (/etc/rc.d/rc.sysinit)
6. daemon start (/etc/rc.d/rc[0-6].d/*)
7. local setting (/etc/rc.d/rc.local)
```

每个系统启动级别的守护进程位置不一致，通常有以下位置

```shell
/etc/rc.d
/etc/rc[N].d
```
知道了守护进程的位置，就可以把 test1 放在 /etc/rc5.d/ 下，并且还要改名称，因为系统需要根据指定的名称来使用 for 循环来启动或者关闭每个程序，命名规则如下： 
1. S[num][name]：启动守护进程name，例如：```S01test1```
2. K[num][name]：禁止启动守护进程name，例如：```K01test1```

我们这里肯定要启动了，所以将 ```test1``` 命名为 ```S01test1```：
```shell
mv test1 /etc/rc5.d/S01test1
```
