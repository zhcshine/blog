---
title: ffmpeg音频基础
date: 2019-07-24 09:11:21
tags:
  - ffmpeg
categories:
---

2# 音频知识（未完工）

# 声音的本质

> 声音其实是一种能量波，因此也有频率和振幅的特征，频率对应于时间轴线，振幅对应于电平轴线。波是无限光滑的，弦线可以看成由无数点组成，由于存储空间是相对有限的，数字编码过程中，必须对弦线的点进行采样。

# [声音三要素](https://baike.baidu.com/item/%E5%A3%B0%E9%9F%B3%E5%90%AC%E8%A7%89)

1. 音高/音调/音频： 客观上音高大小主要取决于声波基频的高低，频率高则音调高，反之则低，单位用赫兹(Hz）表示。儿童(高音区)->女生(高音区)->男生（低音区）

2. 音量/响度/声强：常说的声音大小，主要取决于声波振幅的大小。
3. 音色/音品：和材质相关（比如钢琴等），本质是谐波。

![音量和声调](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/%E9%9F%B3%E9%87%8F%E5%92%8C%E5%A3%B0%E8%B0%83.png)

心理声学

> 人类的听觉范围：20Hz-20kHz

# 音频量化过程

![音频量化过程](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/%E9%9F%B3%E9%A2%91%E9%87%8F%E5%8C%96%E8%BF%87%E7%A8%8B.png)

1. 按横轴时间进行切割
2. 按纵轴振幅(决定音量)进行切割
3. 编码转成数字信号

> 采样大小：一个采样用多个bit存放，常用16位(2<sup>16</sup>=65535)，振幅
> 采样率：采样频率8k、16k、32k、44.1k、48k等 [采样率概念](https://baike.baidu.com/item/音频采样率)
> 声道数：单声道、双声道、多声道



码率计算
> 音频流的码率=采样率×采样大小×声道数
> 例如：采样率44.1kHz、采样大小为16bit、双声道的pcm编码wav文件，码率=44.1k×16×2=1411.2kb/s



# 音频压缩技术

1. 消除音频冗余数据

> 删除人耳听觉范围外的音频信号：
>
> > 20Hz-20kHz
>
> 被掩避掉的音频信号： 
>
> > 频域遮蔽：
> >
> > 时域遮蔽：
>
> 

2. 哈夫曼无损编码

# 音频编解码器

常见的编解码器

> OPUS： 开源、rmtp协议不支持
> AAC：收费、适合实时互动项目。
> Vorbis：开源
> Speex：开源、优势：还支持一些回音消除、降噪等处理办法
> iLBC
> AMR
> G.711：与固定电话就行融合

性能对比：

> OPUS > AAC(常用) > Vorbis
> AAC： advanced audio coding
> 常见规格： AAC LC（低复杂度，码流128k）、AAC HE V1（）、 AAC HE V2
> AAC LC + SBR = AAC HE V1
> AAC HE V1 + PS = AAC HE V2

![aac规格](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/aac%E8%A7%84%E6%A0%BC.png)

AAC格式：
> ADIF
> ADTS

AAC编码库：

> libfdk_aac(常用) > ffmpeg aac > libfaac > libvo_aacenc