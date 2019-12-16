---
title: Excel公式入门基础
categories:
- 办公
date: 2016-11-09 15:14:08
tags:
- Excel
- Office
---

### 返回最后一个值、倒数第二个值
```textmate
=LOOKUP(1,0/(A2:J2<>""),A2:J2)
=LOOKUP(1,0/(B2:J2<>""),A2:J2)
```

### 返回时间差
```textmate
=DATEDIF(--TEXT(F2,"00-00-00"),TODAY(),"D")
```

### VLOOKUP 逆向检索

[参考](http://zhidao.baidu.com/link?url=-1b12Kx36WQ4vf05DIDhZmxCOtvC6Zdhc62kve1QRoF-hV_Hbl3cZDuBZOYgGRjEo513fDk9-UL6gfUnMI9Jna)
```textmate
=VLOOKUP(C2,IF({1,0},Sheet2!$D$2:$D$5,Sheet2!$A$2:$A$5),2,FALSE)
=INDEX(Sheet2!A:A,MATCH(Sheet1!C2,Sheet2!D:D,0))
```
    

### COUNTIFS 多条件求和 
![示例](http://7xq3ry.com1.z0.glb.clouddn.com/20160304142206.png)
```textmate
统计每个部门人数
{=SUM(IFERROR(1/(COUNTIFS(Sheet2!L$2:Sheet2!L$250,B5,Sheet2!N$2:Sheet2!N$250,Sheet2!N$2:Sheet2!N$250)*(Sheet2!L$2:Sheet2!L$250=B5)),))}
```
