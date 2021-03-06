---
title: Ffmpeg入门基础
date: 2019-07-24 09:16:46
tags:
- Ffmpeg
- C
- C++
categories:
- 后端
---

## Ffmpeg基本流程
```sequence
文件->音视频流: demuxer解封装
音视频流->数据包: decoder解码
数据包->音视频流: encoder编码
音视频流->文件: muxer封装
```

## Ffmpeg基本定义

1. 文件：也叫容器Container，比如mp4文件、flv文件等，容器的类型决定了信息被存放在文件中的位置。
2. 音视频流： 

## Ffmpeg录制
### 录制设备查询
```shell
➜  $ ffmpeg -hide_banner -devices
Devices:
 D. = Demuxing supported
 .E = Muxing supported
 --
 DE alsa            ALSA audio output
 DE fbdev           Linux framebuffer
 D  iec61883        libiec61883 (new DV1394) A/V input device
 D  jack            JACK Audio Connection Kit
 D  kmsgrab         KMS screen capture
 D  lavfi           Libavfilter virtual input device
 DE oss             OSS (Open Sound System) playback
 DE pulse           Pulse audio output
  E sdl,sdl2        SDL2 output device
 DE video4linux2,v4l2 Video4Linux2 output device
 D  x11grab         X11 screen capture, using XCB
  E xv              XV (XVideo) output device
```
* D：输入设备
* E：输出设备 

[ffmpeg输入设备列表](<https://ffmpeg.org/ffmpeg-devices.html#Input-Devices>)

*<font color="#2bbc8a">ALSA</font>*是Advanced Linux Sound Architecture的缩写，高级Linux声音架构的简称,它在Linux操作系统上提供了音频和MIDI（Musical Instrument Digital Interface，音乐设备数字化接口）的支持，需要安装libasound库

*<font color="#2bbc8a">android_camera</font>*Android相机输入设备。 此输入设备使用Android Camera2 NDK API，该API可在API级别为24+的设备上使用。在配置期间自动检测android_camera的可用性。 该设备允许从Android设备上的所有摄像头进行捕获，这些摄像头集成在Camera2 NDK API中。 可用的摄像机在内部枚举，可以使用camera_index参数进行选择。输入文件字符串被丢弃。 通常，后置摄像头的索引为0，而前置摄像头的索引为1。

*<font color="#2bbc8a">avfoundation</font>*音视频 苹果设备

*<font color="#2bbc8a">bktr</font>* BSD 视频输入设备

*<font color="#2bbc8a">decklink</font>* BlackMagic Decklink是一款影像采集编辑产品，接口类型PCI-33/66、PCI-X 133&100MHz 。

*<font color="#2bbc8a">dshow</font>* Windows DirectShow input device.

*<font color="#2bbc8a">fbdev</font>* Linux framebuffer（Linux帧缓冲）输入设备，只能录制终端的视频
Linux framebuffer是一种独立于硬件的图像抽象层，它用于在计算机屏幕上显示图像,通常是在控制台（环境）。它可以通过一个文件设备节点访问，通常为：`/dev/fb0`

*<font color="#2bbc8a">gdigrab</font>* Win32 GDI 屏幕截取设备, 这个设备允许你截取显示在Windows（系统）上的屏幕区域。

*<font color="#2bbc8a">iec61883</font>* 使用`iec61883`的FireWire（火线） DV/HDV输入设备。
要允许这个输入设备，需要`libiec61883`, `libraw1394` 和 `libavc1394`被安装到系统中。此外还要在编译时配置`--enable-libiec61883`以支持。

*<font color="#2bbc8a">jack</font>* JACK输入设备。为了能使用JACK设备，需要系统上存在`libjack`

*<font color="#2bbc8a">kmsgrab</font>*

*<font color="#2bbc8a">lavfi</font>* `Libavfilter`输入虚拟设备, 这个输入设备可以从`libavfilter`滤镜链图的一个开放输出端口读取数据。
对于每个滤镜链图开放输出端口，这个输入设备将创建一个对应的流映射到这个端口进行输出。当前只支持视频数据。滤镜链图是通过选项`graph`描述的。

*<font color="#2bbc8a">libcdio</font>* 基于`libcdio`的音乐CD输入设备。需要系统中有`libcdio`才能启用，且编译时需要用`--enable-libcdio`配置选项允许。

*<font color="#2bbc8a">libdc1394</font>* IIDC1394输入设备，其基于`libdc1394`和`libraw1394`, 编译允许需要配置`--enable-libdc1394`

*<font color="#2bbc8a">openal</font>* 这个OpenAL输入设备支持在所有实现了`OpenAL 1.1`的系统上进行音频捕获。
要编译使用它需要系统包含`OpenAL`头和`libraries`库，并且设置编译选项`--enable-openal`

*<font color="#2bbc8a">oss</font>*

*<font color="#2bbc8a">pulse</font>* PulseAudio（脉冲音频）输入设备, 要使用须编译配置设置`--enable-libpulse`

*<font color="#2bbc8a">sndio</font>* sndio输入设备。要使用它需要系统安装并配置有`libsndio`库

*<font color="#2bbc8a">video4linux2, v4l2</font>* Video4Linux2 输入视频设备, "v4l2"是"video4linux2"的别名
编译需要`v4l-utiles`支持（`--enable-libv4l2`编译选项被配置），也可用于`-use_libv4l2`输入设备选项。
捕获的设备名是一个文件设备节点，通常Linux系统在设备（例如USB摄像头）插入到系统时自动创建这样的节点，会被命名为`/dev/videoN`，`N`是设备索引序号
Video4Linux设备通常只支持有限的分辨率（`width x height`）和帧率,通过`-list_formats all`选项来获取支持情况。一些设备，例如电视卡可以支持1个或者多个标准，它支持的标准可以通过`-list_standards all`来了解。
时间戳时基单位为1microsecond。根据内核版本和配置，时间戳可以基于实时间（real time clock——绝对时间，一种起源于Unix的表示方式）或者单调时钟（monotonic clock——通常源自启动时间，不受NTP或者手动改变）。`-timestamps abs`或者`-ts abs`选择启用实时间。

*<font color="#2bbc8a">vfwcap</font>* vfw（Video for Windows）捕获输入端
文件名必须是捕获设备索引，范围0-9可以用`list`作为文件名，将输出一个设备列表。任何数字外其它文件名被视作设备索引0。

*<font color="#2bbc8a">x11grab</font>* X11 视频输入设备, 使用需要`libxcb`库，它会在编译时自动检测。

### fbdev
*linux终端录制设备*

查看使用指定采集设备参数
```shell
$ ffmpeg -hide_banner -h demuxer=fbdev
Demuxer fbdev [Linux framebuffer]:
fbdev indev AVOptions:
  -framerate         <video_rate> .D.......  (default "25")
# framerate 帧数 默认25帧
```
```shell
$ sudo ffmpeg -framerate 30  -f fbdev -i /dev/fb0 output.mp4
```

### v412
```shell
$ ffmpeg -hide_banner -h demuxer=Video4Linux2
```
| 参数            | 类型     | 说明                         |
| --------------- | -------- | ---------------------------- |
| -standard       | 字符串   | 设置tv标准                   |
| -channel        | 整数     | 设置tv通道                   |
| -video_size     | 图像大小 | 设置采集视频频帧大小         |
| -pixel_format   | 字符串   | 设置采集视频的分辨率         |
| -input_format   | 字符串   | 设置采集视频的分辨率         |
| -framerate      | 字符串   | 设置采集视频帧率             |
| -list_formats   | 整数     | 列举输入视频信号的信息       |
| -list_standards | 整数     | 列举标准信息                 |
| -timestamps     | 整数     | 设置时间戳                   |
| -ts             | 整数     | 设置模拟器分析帧使用的时间戳 |
| -use_libv4l2    | 布尔     | 使用第三方库libv412选项      |

```shell
$ ffmpeg -hide_banner -f Video4Linux2 -list_formats all -i /dev/video0
# /dev/video0根据自己的摄像头来设置
```

### x11grab
*linux桌面录制设备*

[桌面录制方法](<https://trac.ffmpeg.org/wiki/Capture/Desktop>)

linux环境下如果此方法报错
```shell
Cannot open display :0.0, error 1.
:0.0: Input/output error
```
则需要查看$DISPLAY， 在Linux/Unix类操作系统上, DISPLAY用来设置将图形显示到何处. 直接登陆图形界面或者登陆命令行界面后使用startx启动图形, DISPLAY环境变量将自动设置为:0:0
```shell
$ echo $DISPLAY
:1
```
```shell
$ ffmpeg -video_size 1024x768 -framerate 25 -f x11grab -i :1+100,200 output.mp4
```
* video_size 字符串 输入采集视频的分辨率
* framerate 字符串 输入采集的视频帧率
* -f 指定输入设备
* -i 指定输入内容（此处为显示器）+100, 200 代表从100, 200处位移

使用pulse录制音频
```shell
$ pactl list sources  # 查看全部输入设备
```
```shell
$ ffmpeg -f pulse -i default pulse.wav
```
同时录制视频和音频
```shell
$ ffmpeg -video_size 1366x768 -framerate 25 -f x11grab -i :1 -f pulse -ac 2 -i 1 output.mkv
```
```shell
$ ffmpeg -video_size 1024x768 -framerate 25 -f x11grab -i :1+100,200 -f alsa -ac 2 -i hw:0 output.mkv
```

## Ffmpeg直播
### 推流
```shell
$ ffmpeg -re -i out.mp4 -c copy -f flv rtmp://server/live/streamName
```
* -re 保持本地视频和流媒体服务器帧率一致
* -c 不做操作，直接复制
* -f 格式

### 拉流
```shell
$ ffmpeg -i rmtp://server/live/streamName -c copy dump.flv
```


## Ffmpeg封装与解封装
```shell
$ ffmpeg -i input.mkv -vcodec copy -acodec copy output.mp4
```
* -i 输入文件
* -vcodec copy 视频编码处理方式
* -acodec copy 音频编码处理方式

```shell
$ ffmpeg -i input.mp4 -vn -acodec copy output.aac  # 抽出音频
$ ffmpeg -i input.mp4 -vcodec copy -an output.h264  # 抽出视频
```

### 提取YUV数据
```shell
$ ffmpeg -i input.mp4 -an -c:v rawvideo -pix_fmt yuv420p out.yuv
```

* -i 输入设备
* -an 不需要音频
* -c:v 使用rawvideo对视频进行编码
* -pix_fmt 像素格式

*播放源文件*
```shell
$ ffplay -video_size 1366x768 out.yuv
```
* -video_size 指定像素

### 提取PCM数据
```shell
$ ffmpeg -i input.mp4 -vn -ar 44100 -ac 2 -f s16le out.pcm
```

* -i 输入设备
* -vn 不需要视频
* -ar 44100 -a 音频 read 音频采样率44.1k
* -ac 2 -a 音频 c2 channel 2 双声道
* -f 数据存储格式 s16le   s: 有符号的 

*播放源文件*
```shell
$ ffplay -ar 44100 -ac 2 -f s16le out.pcm
```

## Ffmpeg滤镜
### 裁剪滤镜
```shell
$ ffmpeg -i output.mkv -vf crop=in_w-400:in_h-200 -c:v libx264 -c:a copy output.mp4
```
* -i 输入
* -vf -v 视频 f 滤镜 crop 裁剪滤镜
* -c:v
* -c:a copy 音频直接复制

### 视频裁剪
```shell
$ ffmpeg -i output.mkv -ss 00:00:00 -t 10 output.mp4
```
* -ss 开始时间(时:分:秒)
*-t 时常(秒)

### 视频合并
```shell
$ ffmpeg -f concat -i inputs.txt output.mp4
```
* -f  concat 拼接
* -i 输入文件 inputs.txt 记录全部等待拼接的文件
* inputs.txt每一行为“file filename”
* file 'out1.mp4'
* file 'out2.mp4'
* output.mp4 输出文件

### 视频转图片
```shell
$ ffmpeg -i output.mp4 -r 1 -f image2 image-%3d.jpeg
```
* -i 输入文件
* -r 1, r 帧率， 1 每秒一张图片
* -f image2, 转成image2格式
* image-%3d.jpeg 输出文件

### 图片转视频
```shell
$ ffmpeg -i image-%3d.jpeg output.mp4
```