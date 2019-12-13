---
title: ffmpeg封装与解封装基础
date: 2019-07-24 09:20:23
tags:
  - ffmpeg
---
# ffmpeg封装与解封装

```
$ ffmpeg -i input.mkv -vcodec copy -acodec copy output.mp4
```

> -i 输入文件
>
> -vcodec copy 视频编码处理方式
>
> -acodec copy 音频编码处理方式

```
$ ffmpeg -i input.mp4 -vn -acodec copy output.aac  # 抽出音频
$ ffmpeg -i input.mp4 -vcodec copy -an output.h264  # 抽出视频
```

## 提取YUV数据

```
$ffmpeg -i input.mp4 -an -c:v rawvideo -pix_fmt yuv420p out.yuv
```

> -i 输入设备
>
> -an 不需要音频
>
> -c:v 使用rawvideo对视频进行编码
>
> -pix_fmt 像素格式

播放源文件

```
ffplay -video_size 1366x768 out.yuv
```

> -video_size 指定像素

## 提取PCM数据

```
$ ffmpeg -i input.mp4 -vn -ar 44100 -ac 2 -f s16le out.pcm
```

> -i 输入设备
>
> -vn 不需要视频
>
> -ar 44100 -a 音频 read 音频采样率44.1k
>
> -ac 2 -a 音频 c2 channel 2 双声道
>
> -f 数据存储格式 s16le   s: 有符号的 

播放源文件

```
$ ffplay -ar 44100 -ac 2 -f s16le out.pcm
```

