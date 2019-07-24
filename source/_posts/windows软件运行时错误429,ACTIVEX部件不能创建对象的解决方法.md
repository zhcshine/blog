---
title: windows软件运行时错误429,ACTIVEX部件不能创建对象的解决方法
url: 70.html
id: 70
comments: false
categories:
  - 运维
date: 2016-11-09 15:38:56
tags:
---

### 安装软件出现“ACTIVEX部件不能创建对象”

提示错误

> 中文：“运行时错误429，ActiveX部件不能创建对象”  
> 英文："Run-time error '429' ActiveX componnent can't create object"

新建文件，将下面代码复制粘贴，文件另存为xxx.bat 运行xxx.bat，重启电脑，OK.

    echo 正在修复，这个过程可能需要几分钟，请稍候…… 
    rundll32.exe advpack.dll /DelNodeRunDLL32 %systemroot%\System32\dacui.dll 
    rundll32.exe advpack.dll /DelNodeRunDLL32 %systemroot%\Catroot\icatalog.mdb 
    regsvr32 /s comcat.dll 
    regsvr32 /s asctrls.ocx 
    regsvr32 /s oleaut32.dll 
    regsvr32 /s shdocvw.dll /I 
    regsvr32 /s shdocvw.dll 
    regsvr32 /s browseui.dll 
    regsvr32 /s browseui.dll /I 
    regsvr32 /s msrating.dll 
    regsvr32 /s mlang.dll 
    regsvr32 /s hlink.dll 
    regsvr32 /s mshtml.dll 
    regsvr32 /s mshtmled.dll 
    regsvr32 /s urlmon.dll 
    regsvr32 /s plugin.ocx 
    regsvr32 /s sendmail.dll 
    regsvr32 /s mshtml.dll /i 
    regsvr32 /s scrobj.dll 
    regsvr32 /s corpol.dll 
    regsvr32 /s jscrīpt.dll 
    regsvr32 /s msxml.dll 
    regsvr32 /s imgutil.dll 
    regsvr32 /s cryptext.dll 
    regsvr32 /s inseng.dll 
    regsvr32 /s iesetup.dll /i 
    regsvr32 /s cryptdlg.dll 
    regsvr32 /s actxprxy.dll 
    regsvr32 /s dispex.dll 
    regsvr32 /s occache.dll 
    regsvr32 /s iepeers.dll 
    regsvr32 /s urlmon.dll /i 
    regsvr32 /s cdfview.dll 
    regsvr32 /s webcheck.dll 
    regsvr32 /s mobsync.dll 
    regsvr32 /s pngfilt.dll 
    regsvr32 /s licmgr10.dll 
    regsvr32 /s hhctrl.ocx 
    regsvr32 /s inetcfg.dll 
    regsvr32 /s trialoc.dll 
    regsvr32 /s tdc.ocx 
    regsvr32 /s MSR2C.DLL 
    regsvr32 /s msident.dll 
    regsvr32 /s msieftp.dll 
    regsvr32 /s xmsconf.ocx 
    regsvr32 /s ils.dll 
    regsvr32 /s msoeacct.dll 
    regsvr32 /s wab32.dll 
    regsvr32 /s wabimp.dll 
    regsvr32 /s wabfind.dll 
    regsvr32 /s oemiglib.dll 
    regsvr32 /s directdb.dll 
    regsvr32 /s inetcomm.dll 
    regsvr32 /s msoe.dll 
    regsvr32 /s oeimport.dll 
    regsvr32 /s msdxm.ocx 
    regsvr32 /s dxmasf.dll 
    regsvr32 /s laprxy.dll 
    regsvr32 /s l3codecx.ax 
    regsvr32 /s acelpdec.ax 
    regsvr32 /s mpg4ds32.ax 
    regsvr32 /s danim.dll 
    regsvr32 /s Daxctle.ocx 
    regsvr32 /s lmrt.dll 
    regsvr32 /s datime.dll 
    regsvr32 /s dxtrans.dll 
    regsvr32 /s dxtmsft.dll 
    regsvr32 /s wshom.ocx 
    regsvr32 /s wshext.dll 
    regsvr32 /s vbscrīpt.dll 
    regsvr32 /s scrrun.dll mstinit.exe /setup 
    regsvr32 /s msnsspc.dll /SspcCreateSspiReg 
    regsvr32 /s msapsspc.dll /SspcCreateSspiReg 
    echo. 
    echo. 
    echo 修复成功！任意键退出！ 
    pause>nul