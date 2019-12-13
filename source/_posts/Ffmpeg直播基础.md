---
title: ffmpeg直播基础
date: 2019-07-24 09:19:22
tags:
  - ffmpeg
---

## 直播推流

```
$ ffmpeg -re -i out.mp4 -c copy -f flv rtmp://server/live/streamName
```

> -re 保持本地视频和流媒体服务器帧率一致
>
> -c 不做操作，直接复制
>
> -f 格式

## 直播拉流

```
$ ffmpeg -i rmtp://server/live/streamName -c copy dump.flv
```

