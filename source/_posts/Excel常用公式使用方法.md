---
title: Excel常用公式使用方法
url: 61.html
id: 61
comments: false
categories:
  - 办公
date: 2016-11-09 15:14:08
tags:
---

### 返回最后一个值、倒数第二个值

    =LOOKUP(1,0/(A2:J2<>""),A2:J2)
    =LOOKUP(1,0/(B2:J2<>""),A2:J2)
    

### 返回时间差

    =DATEDIF(--TEXT(F2,"00-00-00"),TODAY(),"D")
    

### VLOOKUP 逆向检索

> http://zhidao.baidu.com/link?url=-1b12Kx36WQ4vf05DIDhZmxCOtvC6Zdhc62kve1QRoF-hV_Hbl3cZDuBZOYgGRjEo513fDk9-UL6gfUnMI9Jna

    =VLOOKUP(C2,IF({1,0},Sheet2!$D$2:$D$5,Sheet2!$A$2:$A$5),2,FALSE)
    =INDEX(Sheet2!A:A,MATCH(Sheet1!C2,Sheet2!D:D,0))
    

### 多条件统计求和 COUNTIFS

统计每个部门人数

    {=SUM(IFERROR(1/(COUNTIFS(Sheet2!L$2:Sheet2!L$250,B5,Sheet2!N$2:Sheet2!N$250,Sheet2!N$2:Sheet2!N$250)*(Sheet2!L$2:Sheet2!L$250=B5)),))}
    

![](http://7xq3ry.com1.z0.glb.clouddn.com/20160304142206.png)