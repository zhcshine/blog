---
title: linux命令记录
url: 219.html
id: 219
comments: false
categories:
  - 运维
date: 2017-11-08 14:41:40
tags:
---

### 查看文件夹大小

    du -h your_path --max-depth=1
    

#### 常见命令

> -a或-all 为每个指定文件显示磁盘使用情况，或者为目录中每个文件显示各自磁盘使用情况。 -b或-bytes 显示目录或文件大小时，以byte为单位。 -c或–total 除了显示目录或文件的大小外，同时也显示所有目录或文件的总和。 -D或–dereference-args 显示指定符号连接的源文件大小。 -h或–human-readable 以K，M，G为单位，提高信息的可读性。 -H或–si 与-h参数相同，但是K，M，G是以1000为换算单位,而不是以1024为换算单位。 -k或–kilobytes 以1024 bytes为单位。 -l或–count-links 重复计算硬件连接的文件。 -L<符号连接>或–dereference<符号连接> 显示选项中所指定符号连接的源文件大小。 -m或–megabytes 以1MB为单位。 -s或–summarize 仅显示总计，即当前目录的大小。 -S或–separate-dirs 显示每个目录的大小时，并不含其子目录的大小。 -x或–one-file-xystem 以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过。 -X<文件>或–exclude-from=<文件> 在<文件>指定目录或文件。 –exclude=<目录或文件> 略过指定的目录或文件。 –max-depth=<目录层数> 超过指定层数的目录后，予以忽略。 –help 显示帮助。 –version 显示版本信息