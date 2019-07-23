---
title: PHP将PDF转为jpg，png图片
tags:
  - php
url: 14.html
id: 14
comments: false
categories:
  - 前端
date: 2016-11-08 16:23:41
---

### 环境

WINDOW,PHP5.2

### 安装

安装ImageMagick并添加环境变量 [官网](http://www.imagemagick.org/script/index.php)  
安装php扩展php_imagick.dll,并在php.ini开启 [官网](https://pecl.php.net/package/imagick)  
查看是否载入扩展

    phpinfo();
    

安装Ghostscript并添加环境变量 [官网](http://www.ghostscript.com/download/)

### 下载地址

[百度网盘](http://pan.baidu.com/s/1kTOkhFL)

### 测试是否可以正常运行

    λ convert.exe SEZ588Hu.pdf SEZ588Hu.png
    

### php代码

生成jpg文件

    $im = new imagick('A.pdf[0]');
    $im->setImageFormat('jpg');
    $im->writeImages('A.jpg', true);
    
    

或者直接输出到浏览器

    <?php
    $im = new imagick('file.pdf[0]');
    $im->setImageFormat('jpg');
    header('Content-Type: image/jpeg');
    echo $im;
    ?>
    

### 错误处理

1，将Ghostscript64位卸载，安装32位  
2，将php.ini里的upload\_tmp\_dir对应目录权限修改为IISUER可读写