---
title: Wireshark抓包基础
categories:
- 运维
date: 2016-11-09 15:09:21
tags:
- Wireshark
---

### Nmap安装
```shell
$ sudo apt-get install nmap
```
    

### Zenmap图形GUI安装
```shell
$ sudo apt-get install Zenmap
```
    

### 扫描主机
```shell
$ nmap -T4 -A -v 192.168.1.3/16
```
    
其中-A选项用于使用进攻性（Aggressive）方式扫描；-T4指定扫描过程使用的时序（Timing），总有6个级别（0-5），级别越高，扫描速度越快，但也容易被防火墙或IDS检测并屏蔽掉，在网络通讯状况良好的情况推荐使用T4；-v表示显示冗余（verbosity）信息，在扫描过程中显示扫描的细节，从而让用户了解当前的扫描状态。  

* /32 表示扫描 192.168.1.3  
* /24 表示扫描 192.168.1.0-192.168.1.255  
* /16 表示扫描 192.168.0.0-192.168.255.255  
* /8  
* /1  

[参考cird](https://zh.wikipedia.org/wiki/%E6%97%A0%E7%B1%BB%E5%88%AB%E5%9F%9F%E9%97%B4%E8%B7%AF%E7%94%B1)

### Nmap所识别的6个端口状态

<font color="#2bbc8a">open(开放的)</font> 

应用程序正在该端口接收TCP 连接或者UDP报文。发现这一点常常是端口扫描 的主要目标。安全意识强的人们知道每个开放的端口 都是攻击的入口。攻击者或者入侵测试者想要发现开放的端口。 而管理员则试图关闭它们或者用防火墙保护它们以免妨碍了合法用户。 非安全扫描可能对开放的端口也感兴趣，因为它们显示了网络上那些服务可供使用。 

<font color="#2bbc8a">closed(关闭的)</font>

关闭的端口对于Nmap也是可访问的(它接受Nmap的探测报文并作出响应)， 但没有应用程序在其上监听。 它们可以显示该IP地址上(主机发现，或者ping扫描)的主机正在运行up 也对部分操作系统探测有所帮助。 因为关闭的关口是可访问的，也许过会儿值得再扫描一下，可能一些又开放了。 系统管理员可能会考虑用防火墙封锁这样的端口。 那样他们就会被显示为被过滤的状态，下面讨论。 

<font color="#2bbc8a">filtered(被过滤的)</font>

由于包过滤阻止探测报文到达端口， Nmap无法确定该端口是否开放。过滤可能来自专业的防火墙设备，路由器规则 或者主机上的软件防火墙。这样的端口让攻击者感觉很挫折，因为它们几乎不提供 任何信息。有时候它们响应ICMP错误消息如类型3代码13 (无法到达目标: 通信被管理员禁止)，但更普遍的是过滤器只是丢弃探测帧， 不做任何响应。 这迫使Nmap重试若干次以访万一探测包是由于网络阻塞丢弃的。 这使得扫描速度明显变慢。 

<font color="#2bbc8a">unfiltered(未被过滤的)</font>

未被过滤状态意味着端口可访问，但Nmap不能确定它是开放还是关闭。 只有用于映射防火墙规则集的ACK扫描才会把端口分类到这种状态。 用其它类型的扫描如窗口扫描，SYN扫描，或者FIN扫描来扫描未被过滤的端口可以帮助确定 端口是否开放。 

<font color="#2bbc8a">open|filtered(开放或者被过滤的)</font>

当无法确定端口是开放还是被过滤的，Nmap就把该端口划分成 这种状态。开放的端口不响应就是一个例子。没有响应也可能意味着报文过滤器丢弃 了探测报文或者它引发的任何响应。因此Nmap无法确定该端口是开放的还是被过滤的。 UDP，IP协议， FIN，Null，和Xmas扫描可能把端口归入此类。 

<font color="#2bbc8a">closed|filtered(关闭或者被过滤的)</font>

该状态用于Nmap不能确定端口是关闭的还是被过滤的。 它只可能出现在IPID Idle扫描中。

### 监视指定网络接口的数据包tcpdump

如果不指定网卡，默认tcpdump只会监视第一个网络接口，一般是eth0，
```shell
$ tcpdump -i eth0
```
    
### 路由追踪traceroute

linux
```shell
$ sudo apt-get install traceroute  
$ traceroute hostname
```
window
```shell
tracert hostname
```
### ubuntu开启和关闭混杂模式

```shell
ifconfig eth0 promisc # 设置eth0为混杂模式。 
ifconfig eth0 -promisc # 取消它的混杂模式
```

ifconfig参数详解

* <font color="#2bbc8a">eth0/eth1</font> 分别表示第一块网卡和第二块网卡； 
* <font color="#2bbc8a">lo</font> 表示回环测试接口，注意，这个虚拟的interface一定要存在！ 
* <font color="#2bbc8a">usb0</font> 表示USB接口的网卡； 
* <font color="#2bbc8a">Linkencap</font> 表示位于OSI物理层的名称，即连接类型Ethernet（以太网） 
* <font color="#2bbc8a">HWaddr</font> 表示网卡的硬件地址，即MAC地址 
* <font color="#2bbc8a">inetaddr</font> 表示网卡IP； 
* <font color="#2bbc8a">Bcast</font> 表示广播(broadcast ) 的地址； 
* <font color="#2bbc8a">Mask</font> 表示子网掩码； 
* <font color="#2bbc8a">UP</font> 表示网卡开启状态； 
* <font color="#2bbc8a">BROADCAST</font> 表示支持广播； 
* <font color="#2bbc8a">MULTICAST</font> 表示网卡混杂模式； 
* <font color="#2bbc8a">RUNNING</font> 表示网卡的网线被接上； 
* <font color="#2bbc8a">MULTICAST</font> 表示支持组播； 
* <font color="#2bbc8a">MTU</font> 表示MaximumTrasmission Unit 最大传输单元(字节)，即此接口一次所能传输的最大封包； 
* <font color="#2bbc8a">Metric</font> 表示路由度量值，缺省值是0； 
* <font color="#2bbc8a">RX</font> 表示网络由激活到目前为止接收的数据包； 
* <font color="#2bbc8a">TX</font> 表示网络由激活到目前为止发送的数据包； 
* <font color="#2bbc8a">Collisions</font> 表示网络信号冲突的情况； 
* <font color="#2bbc8a">Txqueuelen</font> 表示传输缓冲区长度大小； 
* <font color="#2bbc8a">Memory</font> 表示占用内存范围。

### 安装wireshark
```shell
$ sudo apt-get install wireshark
```

**wireshark捕获过滤器**  

* src 192.168.0.10 && port 80 源地址192.168.0.10和端口为80相关  
* host 192.160.0.1 和主机192.168.0.1相关  
* src host 192.168.0.1  
* port 8080  
* !port 8080  
* dst port 80  
* icmp  
* !ip6

**wireshark显示过滤器**

* !tcp  
* ip.addr == 192.168.0.1  
* frame.len <= 128  
* tcp.port == 23 || tcp.port == 21  
* smtp || pop || imap  
* ip.addr == 192.168.0.1 or ip.addr == 192.168.0.2
