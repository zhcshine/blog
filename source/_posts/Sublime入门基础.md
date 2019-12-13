---
title: sublime入门基础
tags:
  - IDE
url: 67.html
id: 67
comments: false
categories:
  - 办公
date: 2016-11-09 15:36:07
---

### 下载地址

[http://www.sublimetext.com/3](http://www.sublimetext.com/3)

### Ubuntu下载

    sudo add-apt-repository ppa:webupd8team/sublime-text-3
    sudo apt-get update
    sudo apt-get install sublime-text-installer
    

### 插件下载地址

[https://packagecontrol.io/](https://packagecontrol.io/)

### 注册码

    ----- BEGIN LICENSE ----
    Andrew Weber
    Single User License
    EA7E-855605
    813A03DD 5E4AD9E6 6C0EEB94 BC99798F
    942194A6 02396E98 E62C9979 4BB979FE
    91424C9D A45400BF F6747D88 2FB88078
    90F5CC94 1CDC92DC 8457107A F151657B
    1D22E383 A997F016 42397640 33F41CFC
    E1D0AE85 A0BBD039 0E9C8D55 E1B89D5D
    5CDB7036 E56DE1C0 EFCC0840 650CD3A6
    B98FC99C 8FAC73EE D2B95564 DF450523
    ------ END LICENSE ------
    

### 安装Package Control

    import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
    
    

### 安装模板Material Theme

配置方法

    Package Control Messages
    ========================
    
    Material Theme
    --------------
    
      Material Theme for Sublime Text 3
    
      For more information on the Material Theme: https://github.com/equinusocio/material-theme
    
      **********************************************************************************************
    
      If installing manually (not through Package Control), add the following to your 
      Settings - User file and restart Sublime Text after:
    
      {
        "theme": "Material-Theme.sublime-theme",
        "color_scheme": "Packages/Material Theme/schemes/Material-Theme.tmTheme",
      }
    
      **********************************************************************************************
    
      This theme includes a Darker and Lighter version:
    
      Darker version:
    
      {
        "theme": "Material-Theme-Darker.sublime-theme",
        "color_scheme": "Packages/Material Theme/schemes/Material-Theme-Darker.tmTheme",
      }
    
      Lighter version:
    
      {
        "theme": "Material-Theme-Lighter.sublime-theme",
        "color_scheme": "Packages/Material Theme/schemes/Material-Theme-Lighter.tmTheme",
      }
    
      **********************************************************************************************
    
      Recommended UI and font settings for a better experience:
    
      {
        "overlay_scroll_bars": "enabled",
        "line_padding_top": 3,
        "line_padding_bottom": 3,
        "always_show_minimap_viewport": true,
        "bold_folder_labels": true,
        "indent_guide_options": [ "draw_normal", "draw_active" ]    // Highlight active indent
        "font_options": [ "gray_antialias" ],                       // For retina Mac
      }
    
      **********************************************************************************************
    
      Other available theme options:
    
      "material_theme_small_tab": true,                           // Set small tabs
      "material_theme_disable_fileicons": true,                   // Hide siderbar file type icons
      "material_theme_disable_folder_animation": true,            // Disable folder animation
      "material_theme_small_statusbar": true,                     // Set small status bar
      "material_theme_disable_tree_indicator": true,              // Disable sidebar file indicator
      "material_theme_bold_tab": true,                            // Make the tab labels bolder
      "material_theme_tabs_separator": true,                      // Show tabs separator
      "material_theme_accent_lime": true,                         // set green lime accent color
      "material_theme_accent_purple": true,                       // set purple accent color
      "material_theme_accent_red": true,                          // set pale red accent color
      "material_theme_accent_orange": true,                       // set orange accent color
      "material_theme_accent_yellow": true,                       // set yellow accent color
      "material_theme_panel_separator": true,                     // show bottom panel separator
      "material_theme_tabs_autowidth": true,                      // Enable autowidth for tabs
      "material_theme_contrast_mode": true,                       // Enable sidebar and panels contrast mode
    

### 设置空格缩进

    {    
        "tab_size": 4,    
        "translate_tabs_to_spaces": true 
    } 
    

### 设置行间距

    {
        "line_padding_bottom": 3, 
        "line_padding_top": 3,
    }    
    

### 插件安装

**Emmet(懂的)**  
**ConvertToUtf8(编码转换)**  
**Sublime​Linter(代码检查工具，需要配合各语言插件包检查)**  
**SideBarEnhancements（侧边栏右键增强）**  
**Bracket​Highlighter（显示标签块？？）**  
**SublimeCodeIntel(跳转到自定义函数)**

> For Windows:  
> Jump to definition = Alt+Click  
> Jump to definition = Control+Windows+Alt+Up  
> Go back = Control+Windows+Alt+Left  
> Manual Code Intelligence = Control+Shift+space  
> 需要自己设置执行路径

            "PHP": {
                "php": "D:\\PHP\\php.exe",
                "codeintel_scan_extra_dir": [],
                "codeintel_scan_files_in_project": true,
                "codeintel_max_recursive_dir_depth": 15,
                "codeintel_scan_exclude_dir":["D:\\PHP"]
            }
    

**HTML5(html5的snippets)**  
**Alignment(自动对齐插件)**  
**SFTP(ftp上传)需要配置对应的ftp路径等信息** **git** **jquery(jquery snippets)** **SaSS(可以支持sass)** **DocBlockr（块状注释）**

### ubuntu安装中文输入法

### 安装

    git clone https://github.com/lyfeyaj/sublime-text-imfix.git
    

### 复制文件

    cd ~/sublime-text-imfix
    sudo cp ./lib/libsublime-imfix.so /opt/sublime_text/
    sudo cp ./src/subl /usr/bin/
    

### 测试

终端输入

    LD_PRELOAD=./libsublime-imfix.so subl
    

### 建立快捷方式

新建文件sublime放在/home目录下，脚本内容：

    #!/bin/bash
    LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so subl
    

### 测试

终端输入

    bash ~/sublime
    

### 快捷方式修改

将\[Desktop Entry\]中的字符串  
Exec=/opt/sublime\_text/sublime\_text %F  
修改为  
Exec=bash -c "LD\_PRELOAD=/opt/sublime\_text/libsublime-imfix.so exec /opt/sublime\_text/sublime\_text %F"  
将\[Desktop Action Window\]中的字符串  
Exec=/opt/sublime\_text/sublime\_text -n  
修改为  
Exec=bash -c "LD\_PRELOAD=/opt/sublime\_text/libsublime-imfix.so exec /opt/sublime\_text/sublime\_text -n"  
将\[Desktop Action Document\]中的字符串  
Exec=/opt/sublime\_text/sublime\_text --command new_file  
修改为  
Exec=bash -c "LD\_PRELOAD=/opt/sublime\_text/libsublime-imfix.so exec /opt/sublime\_text/sublime\_text --command new_file"  
注意：  
修改时请注意双引号"",否则会导致不能打开带有空格文件名的文件。