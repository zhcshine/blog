---
title: ffmpeg视频基础
date: 2019-07-24 09:15:39
tags:
  - ffmpeg
---

# 基本概念

H264基本概念

> I帧： 关键帧、采用帧内压缩技术
> P帧：向前参考帧，采用帧内压缩技术
> B帧：双向参考帧，采用帧内压缩技术

GOF:
> 一组帧group of frame

SPS:
> Sequence parameter set: 序列参数集：存放一组帧的存放帧数，参考帧数目，解码图像尺寸等

PPS：
> picture parameter set: 图像参数集：存放熵编码模式选择标识、片组数目等



# 视频编解码器

> x264：最流行的
> x265：耗性能
> openH264：支持svc视频技术
> vp8: x264
> vp9: x265



# H264压缩技术

> 帧内压缩：解决空域数据冗余技术
>
> 帧间压缩：解决时域数据冗余技术
>
> 整数离散余弦变换(DCT)：将空间上的相关性变为频域上无关的数据然后进行量化
>
> CABAC压缩

![H264结构图](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/H264%E7%BB%93%E6%9E%84%E5%9B%BE.png)

# H264编码分层
> NAL层：network abstraction layer 视频数据网络抽象层
> VCL层：video coding layer 视频数据编码层

# 码流
SODB
> string of data bits 原始数据比特流，它是由vcl层产生的

RBSP
> 在SODB最后一位补1，不按字节对其则补0

EBSP

> 

NALU

> NAL HEADER + EBSP

