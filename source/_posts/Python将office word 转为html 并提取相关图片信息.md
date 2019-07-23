---
title: Python将office word 转为html 并提取相关图片信息
tags:
  - python
url: 29.html
id: 29
comments: false
categories:
  - 办公
  - 运维
date: 2016-11-09 10:10:01
---

### 需求

工作需要将2000多格word文档中的图片提取出来，并改名为word文档名

### 思路1

> More exactly, a .docx document is a Zip archive in OpenXML format: you have first to uncompress it. I downloaded a sample (Google: some search term filetype:docx) and after unzipping I found some folders. The word folder contains the document itself, in file document.xml.

于是使用unzip模块

    import zipfile
    

但是报错

    BadZipfile: File is not a zip file
    

检查发现同事提供的office word文件结尾为.doc,  
于是使用7-zip进行提取发现确实报错

> 文件末端错误

尝试用本机office2013打开并另存为word文档.docx  
再次使用7-zip进行提取，成功，会生成以下一个文件夹

> _rels文件夹  
> docProps文件夹  
> word文件夹  
> \[Content_Types\].xml文件

其中\\word\\media文件夹下就有我需要的图片信息  
既然需要更改文件后缀，那么就有另一个思路。

### 思路2

> 通过win32com.clientm将文件改为html文件，会生成类似的文件夹

核心代码

    # -*- encoding: utf8 -*-
    
    import win32com
    from win32com.client import constants
    
    # 打开初始化word文档
    w = win32com.client.Dispatch('Word.Application')
    w.Visible = 0
    w.DisplayAlerts = 0
    doc = w.Documents.Open(source_file)  # 打开文件
    doc.SaveAs(target_fold + '/' + file_number + '.htm', 8)  # 更名为htm文件
    doc.Close()
    w.Quit()
    
    

另存为其他规格只需要将参数8修改即可

> wdFormatDocument = 0  
> wdFormatDocument97 = 0  
> wdFormatDocumentDefault = 16  
> wdFormatDOSText = 4  
> wdFormatDOSTextLineBreaks = 5  
> wdFormatEncodedText = 7  
> wdFormatFilteredHTML = 10  
> wdFormatFlatXML = 19  
> wdFormatFlatXMLMacroEnabled = 20  
> wdFormatFlatXMLTemplate = 21  
> wdFormatFlatXMLTemplateMacroEnabled = 22  
> wdFormatHTML = 8  
> wdFormatPDF = 17  
> wdFormatRTF = 6  
> wdFormatTemplate = 1  
> wdFormatTemplate97 = 1  
> wdFormatText = 2  
> wdFormatTextLineBreaks = 3  
> wdFormatUnicodeText = 7  
> wdFormatWebArchive = 9  
> wdFormatXML = 11  
> wdFormatXMLDocument = 12  
> wdFormatXMLDocumentMacroEnabled = 13  
> wdFormatXMLTemplate = 14  
> wdFormatXMLTemplateMacroEnabled = 15  
> wdFormatXPS = 18

最后脚本

    # -*- encoding: utf8 -*-
    
    import win32com
    import os
    import xlrd
    import shutil
    from win32com.client import constants
    
    # 从excel表格中导入数据并组成字典
    XlsFile = 'default.xlsx'                  # 需要的数据
    data = xlrd.open_workbook(XlsFile)
    table = data.sheets()[0]
    nrows = table.nrows
    
    # 获取文件名列表
    number_list = []
    for i in range(1, nrows):
        number_list.append(table.row_values(i)[0])
    
    # 打开初始化word文档
    w = win32com.client.Dispatch('Word.Application')
    w.Visible = 0
    w.DisplayAlerts = 0
    
    
    # 遍历文件夹并将文件剪贴到新的文件夹中
    source_fold = os.path.split(os.path.realpath(__file__))[0] + '/' + 'WordFile'
    target_fold = os.path.split(os.path.realpath(__file__))[0] + '/' + 'NewFile'
    img_fold = os.path.split(os.path.realpath(__file__))[0] + '/' + 'Img'
    word_list = os.listdir(source_fold)
    for file_name in word_list:
        # 根据货号在列表中查找
        try:
            file_number = file_name[0:10]
            if file_number in number_list:
                print(file_name + '是需要的文件.')
                source_file = source_fold + '/' + file_name
                doc = w.Documents.Open(source_file)  # 打开文件
                doc.SaveAs(target_fold + '/' + file_number + '.htm', 8)  # 更名为htm文件
                doc.Close()
                od_img = target_fold + '/' + file_number + '.files' + '/' + 'image003.jpg'
                shutil.copyfile(od_img, img_fold + '/' + file_number + '.jpg')  # 复制新的文件
                number_list.remove(file_number)  # 从excel列表中删除此元素防止重复选取
        except Exception, e:
            print(file_name)
            print e
    w.Quit()
    
    try:
        f = open("php.json", "w")
        print >>f, number_list  # 最后将剩下的元素打印到文件中
    except Exception, e:
        print '写入php.json失败'