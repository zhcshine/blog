---
title: ffmpeg命令基础
date: 2019-07-24 09:16:46
tags:
  - ffmpeg
---
#### ffmpeg基本流程

```sequence
文件->音视频流: demuxer解封装
音视频流->数据包: decoder解码
数据包->音视频流: encoder编码
音视频流->文件: muxer封装
```

#### ffmpeg基本定义

1. 文件：也叫容器Container，比如mp4文件、flv文件等，容器的类型决定了信息被存放在文件中的位置。
2. 音视频流： 
