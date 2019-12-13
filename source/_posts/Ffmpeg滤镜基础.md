---
title: ffmpeg滤镜基础
date: 2019-07-24 09:18:29
tags:
---

## 裁剪滤镜

```
$ ffmpeg -i output.mkv -vf crop=in_w-400:in_h-200 -c:v libx264 -c:a copy output.mp4
```

> -i 输入
>
> -vf -v 视频 f 滤镜 crop 裁剪滤镜
>
> -c:v
>
> -c:a copy 音频直接复制

## 视频裁剪

```
$ ffmpeg -i output.mkv -ss 00:00:00 -t 10 output.mp4
```

> -ss 开始时间(时:分:秒)
>
> -t 时常(秒)

## 视频合并

```
$ ffmpeg -f concat -i inputs.txt output.mp4
```

> -f  concat 拼接
>
> -i 输入文件 inputs.txt 记录全部等待拼接的文件
>
> inputs.txt每一行为“file filename”
>
> 	> file 'out1.mp4'
> 	>
> 	> file 'out2.mp4'
>
> output.mp4 输出文件

## 视频转图片

```
$ ffmpeg -i output.mp4 -r 1 -f image2 image-%3d.jpeg
```

> -i 输入文件
>
> -r 1, r 帧率， 1 每秒一张图片
>
> -f image2, 转成image2格式
>
> image-%3d.jpeg 输出文件

## 图片转视频

```
$ ffmpeg -i image-%3d.jpeg output.mp4
```

