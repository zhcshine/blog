---
title: Gtk+入门基础
date: 2019-07-24 13:58:51
tags:
- C
- Gtk+
categories:
- 后端
---

## Gdk+介绍

> Gtk+是c语言编写的用于开发图形界面程序的函数库。Gtk+来源于gimp（gnu image minipulation program），gtk+在gdk（gimp drawing kit）基础上创建，并进行封装。

## Linux安装

```shell
# http://ftp.gnome.org/pub/gnome/sources/gtk+/3.24/
$ wget http://ftp.gnome.org/pub/gnome/sources/gtk+/3.24/gtk+-3.24.10.tar.xz
$ tar xvfJ gtk+-3.24.10.tar.xz
$ ./configure --prefix=/user/include/gtk3.24
$ make && sudo make install
$ sudo ldconfig
# 设置LD_LIBRARY_PATH
$ LD_LIBRARY_PATH="/opt/gtk/lib"
$ PATH="/opt/gtk/bin:$PATH"
$ export LD_LIBRARY_PATH PATH
```

## 教程
### 简单的空白窗口
```c
#include <stdio.h>
#include <gtk/gtk.h>

int main(int argc, char* argv[]) {
    gtk_init(&argc, &argv);
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    gtk_widget_show(window);
    gtk_main();
    return 0;
}
```

```shell
$ gcc -o main main.c `pkg-config --libs -cflags gtk+-3.0`
```

* gtk_init (&argc, &argv); 所有 GTK应用程序都要调用该函数，而且必须在控件定义之前使用，它为我们设置一些缺省值( 例如视觉和颜色 )映射这个函数将函数库初始化，设置缺省的信号处理函数，并检查通过命令行传递给应用程序的参数，自动完成一些必要的初始化工作。

* gtk_main()是在每个Gtk应用程序都要调用的函数。程序运行停在这里等待事件(如键盘事件或鼠标事件)的发生，等待用户来操作窗口

* gtk_window_new()的功能创建一个窗口并返回这个窗口的控件指针。GTK_WINDOW_TOPLEVEL指明窗口的类型为最上层的主窗口，它最常用。接着创建一个GtkWidget *类型的window变量来接收。GtkWidget是GTK+控件类型，GtkWidget * 能指向任何控件的指针类型。

* gtk_widget_show(window)；用来显示上一步创建的窗口控件。在这个简单例子里，所有事件都被忽略。用鼠标点击窗口右上角的“×”按钮也不能将窗口关闭。我们可以在终端敲 Ctrl + C 关闭程序。

### 控件

GTK中控件主要分为两类：容器控件，非容器控件。

容器控件：它可以容纳别的控件，我们可以理解为盒子，盒子拿来装东西。容器控件又分为两类，一类只能容纳一个控件，如窗口，按钮；另一类能容纳多个控件，如布局控件。

非容器控件：它不可以容纳别的控件，如标签、行编辑。

前面我们写的空白窗口是能容纳一个控件的控件，现在我们要做的是，给这个窗口添加一个按钮。

首先，我们需要创建一个按钮，然后需要把按钮添加到窗口，接着，显示按钮控件。

```c
#include <stdio.h>
#include <gtk/gtk.h>

int main(int argc, char* argv[]) {
    // gtk初始化
    gtk_init(&argc, &argv);
    // 创建一个顶层窗口
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    // 创建一个button容器
    GtkWidget *button = gtk_button_new_with_label("hello gtk");
    // 将button放入窗口中，
    gtk_container_add(GTK_CONTAINER(window), button);
    // 显示button
    gtk_widget_show(button);
    // 显示window
    gtk_widget_show(window);
    gtk_main();
    return 0;
}
```

```c
void gtk_container_add(GtkContainer *container, GtkWidget *widget);
```

这里我们是把按钮添加到窗口容器里，widget为按钮指针(以上代码的button)，container为窗口指针(以上代码的window)，这里需要注意的是，我们创建的窗口(window)返回值为GtkWidget *类型，而gtk_container_add()的第一个参数为GtkContainer *类型，由于 GtkWidget *类型的变量为指向任何控件的万能指针，所以，在函数传参时，根据参数类型进行相应的转换，如：

```c
gtk_container_add( (GtkContainer *)window, button);
```

C语言里，我们常用这种方法转换。在GTK中，很多内部变量指针类型转换都定义了宏定义，如：

```c
#define GTK_CONTAINER(x) (GtkContainer *)(x)
```

所以，我们还可以这么写：

```c
gtk_container_add(GTK_CONTAINER(window), button);
```

那么，我们如何知道，哪个类型对应哪个宏定义呢？把要转换类型名字全部变为大写，同时，单词和单词之间以下划线“_”连接，然后，这个名字就是哪个宏定义的名字，如，(GtkButton *)x用宏定义的方法为 GTK_BUTTON(x)。

上面的代码，我们是按钮和窗口都得通过gtk_widget_show()显示出来：

```c
gtk_widget_show(button);// 显示按钮
gtk_widget_show(window);// 显示窗口
```

但是，假如，窗口里有100个控件，还是这样一个一个地显示出来会比较麻烦，这时候，我们可以通过gtk_widget_show_all()显示所有的控件，里面需要传的是容纳控件的那个容器(这里为窗口)。这样，容器上的控件也会跟着显示。

```c
gtk_widget_show_all()
```

### 布局容器-水平布局

如果我们希望窗口里多放添加几个控件，直接添加是不成功的，因为窗口只能容纳一个控件的容器。这时候，我们需要借助布局容器，我们先把布局容器添加到窗口里，然后再把所需要添加的控件放在布局容器里。

布局容器的主要分类：水平布局( GtkHBox)、垂直布局(GtkVBox )、表格布局(GtkTable)、固定布局(GtkFixed )。

**创建一个盒子布局**

```c
// orientation参数: GTK_ORIENTATION_HORIZONTAL GTK_ORIENTATION_VERTICAL 两个可选值 spacing参数是单元格之间的间隙
int gtk_box_new(int orientation, int spacing);
```

以下代码显示水平容器布局方式，垂直容器类似

```c 
#include <stdio.h>
#include <gtk/gtk.h>

int main(int argc, char* argv[]) {
    // gtk初始化
    gtk_init(&argc, &argv);
    // 创建一个顶层窗口
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    // 创建水平容器
    // GtkWidget *hbox = gtk_hbox_new(TRUE, 10);  // gtk+2版本使用
    GtkWidget *hbox = gtk_box_new(GTK_ORIENTATION_HORIZONTAL, 10);  // gtk+3版本使用
    // 将水平容器添加到窗口
    gtk_container_add(GTK_CONTAINER(window), hbox);
    // 创建一个button容器
    GtkWidget *button1 = gtk_button_new_with_label("button1");
    // 将button放入水平容器中
    gtk_container_add(GTK_CONTAINER(hbox), button1);
    // 重复创建两个
    GtkWidget *button2 = gtk_button_new_with_label("button2");
    GtkWidget *button3 = gtk_button_new_with_label("button3");
    gtk_container_add(GTK_CONTAINER(hbox), button2);
    gtk_container_add(GTK_CONTAINER(hbox), button3);
    // 显示全部窗口
    gtk_widget_show_all(window);
    gtk_main();
    return 0;
}
```

### 布局容器-表格布局

**添加表格布局容器**

```c
// rows: 行数, coumns: 列数, homogeneous：容器内表格的大小是否相等, 返回值：表格布局容器指针
GtkWidget *gtk_table_new(guint rows, guint columns, gboolean homogeneous);
```

**布局容器添加控件**

```c
// table: 容纳控件的容器, widget: 要添加的控件, 后四个参数为控件摆放的坐标，规则如下：
void gtk_table_attach_defaults(GtkTable *table, GtkWidget *widget, guint left_attach, guint right_attach, guint top_attach, guint bottom_attach);
```

![](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/gtk%E8%A1%A8%E6%A0%BC%E5%B8%83%E5%B1%80%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

```c
#include <stdio.h>
#include <gtk/gtk.h>

int main(int argc, char* argv[]) {
    // gtk初始化
    gtk_init(&argc, &argv);
    // 创建一个顶层窗口
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    // 创建一个表格容器
    GtkWidget *table = gtk_table_new(2, 2, TRUE); // gtk+2版本
    gtk_container_add(GTK_CONTAINER(window), table);
    // 创建button1， button2, button3
    GtkWidget *button1 = gtk_button_new_with_label("button1");
    GtkWidget *button2 = gtk_button_new_with_label("button2");
    GtkWidget *button3 = gtk_button_new_with_label("button3");
    // 将button放入table中
    gtk_table_attach_defaults(GTK_TABLE(table), button1, 0, 1, 0, 1);
    gtk_table_attach_defaults(GTK_TABLE(table), button2, 1, 2, 0, 1);
    gtk_table_attach_defaults(GTK_TABLE(table), button3, 0, 2, 1, 2);
    // 显示所有窗口
    gtk_widget_show_all(window);
    gtk_main();
    return 0;
}
```

### 布局容器-固定布局

水平、垂直和表格布局容器，控件会跟着容器大小的变化进行自动适应，而固定布局容器里的控件则不会跟着变化( 则固定不变 )。

**固定布局的创建**

```c
GtkWidget *gtk_fixed_new(void);
```

**添加控件**

```c
// fixed：容纳控件的容器, widget：要添加的控件, x, y：控件摆放位置的起点坐标
void gtk_fixed_put(GtkFixed *fixed, GtkWidget *widget, gint x, gint y );
```

**设置控件的大小( 宽和高 )**

```c
// widget：需要设置的控件, width：宽度, height：高度
void gtk_widget_set_size_request(GtkWidget *widget, gint width, gint height);
```

**移动固定布局里控件的位置**

```c
// fixed：固定布局容器, widget：需要移动的控件, x, y: 移动的位置
void gtk_fixed_move(GtkFixed *fixed, GtkWidget *widget, gint x, gint y);
```
示例代码
```c
#include <stdio.h>
#include <gtk/gtk.h>

int main(int argc, char* argv[]) {
    // gtk初始化
    gtk_init(&argc, &argv);
    // 创建一个顶层窗口
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    // 创建一个固定布局容器
    GtkWidget *fixed = gtk_fixed_new();
    // 将fixed添加到window中
    gtk_container_add(GTK_CONTAINER(window), fixed);
    // 创建button
    GtkWidget *button1 = gtk_button_new_with_label("button1");
    GtkWidget *button2 = gtk_button_new_with_label("button2");
    // 将button添加到fixed
    gtk_fixed_put(GTK_FIXED(fixed), button1, 0, 0);
    gtk_fixed_put(GTK_FIXED(fixed), button2, 0, 0);
    // 移动button位置
    gtk_fixed_move(GTK_FIXED(fixed), button2, 150, 150);
    // 设置button大小
    gtk_widget_set_size_request(button2, 100, 50);
    // 显示所有窗口
    gtk_widget_show_all(window);
    gtk_main();
    return 0;
}
```

### 容器布局练习-计算器

**编辑控件( GtkEntry )**
```c
// 创建
GtkWidget * gtk_entry_new(void);
// 编辑
void gtk_entry_set_text (GtkEntry *entry,const gchar *text);
// 设置行编辑是否允许编辑
void gtk_editable_set_editable(GtkEditable *editable, gboolean is_editable);
```
示例代码
```c
#include <stdio.h>
#include <gtk/gtk.h>

int main(int argc, char* argv[]) {
    // 初始化
    gtk_init(&argc, &argv);
    // 创建窗口
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    // 创建5行4列的表格容器
    GtkWidget *table = gtk_table_new(5, 4, TRUE);
    // 将table放入到容器中
    gtk_container_add(GTK_CONTAINER(window), table);
    // 创建一个行编辑
    GtkWidget *entry = gtk_entry_new();
    // 设置行编辑的内容
    gtk_entry_set_text(GTK_ENTRY(entry), "2+2=4");
    // 设置行编辑不允许编辑
    gtk_editable_set_editable(GTK_EDITABLE(entry), FALSE);
    // 创建0-9数字按钮，+-×/=c功能按钮
    GtkWidget *button_0 = gtk_button_new_with_label("0");
    GtkWidget *button_1 = gtk_button_new_with_label("1");
    GtkWidget *button_2 = gtk_button_new_with_label("2");
    GtkWidget *button_3 = gtk_button_new_with_label("3");
    GtkWidget *button_4 = gtk_button_new_with_label("4");
    GtkWidget *button_5 = gtk_button_new_with_label("5");
    GtkWidget *button_6 = gtk_button_new_with_label("6");
    GtkWidget *button_7 = gtk_button_new_with_label("7");
    GtkWidget *button_8 = gtk_button_new_with_label("8");
    GtkWidget *button_9 = gtk_button_new_with_label("9");
    GtkWidget *button_add = gtk_button_new_with_label("+");
    GtkWidget *button_minus = gtk_button_new_with_label("-");
    GtkWidget *button_mul = gtk_button_new_with_label("*");
    GtkWidget *button_div = gtk_button_new_with_label("/");
    GtkWidget *button_equal = gtk_button_new_with_label("=");
    GtkWidget *button_delete = gtk_button_new_with_label("c");
    // 将table放入表格布局中
    gtk_table_attach_defaults(GTK_TABLE(table), entry, 0, 4, 0, 1);
    gtk_table_attach_defaults(GTK_TABLE(table), button_0, 0, 1, 4, 5);
    gtk_table_attach_defaults(GTK_TABLE(table), button_1, 0, 1, 3, 4);
    gtk_table_attach_defaults(GTK_TABLE(table), button_2, 1, 2, 3, 4);
    gtk_table_attach_defaults(GTK_TABLE(table), button_3, 2, 3, 3, 4);
    gtk_table_attach_defaults(GTK_TABLE(table), button_4, 0, 1, 2, 3);
    gtk_table_attach_defaults(GTK_TABLE(table), button_5, 1, 2, 2, 3);
    gtk_table_attach_defaults(GTK_TABLE(table), button_6, 2, 3, 2, 3);
    gtk_table_attach_defaults(GTK_TABLE(table), button_7, 0, 1, 1, 2);
    gtk_table_attach_defaults(GTK_TABLE(table), button_8, 1, 2, 1, 2);
    gtk_table_attach_defaults(GTK_TABLE(table), button_9, 2, 3, 1, 2);
    gtk_table_attach_defaults(GTK_TABLE(table), button_add, 1, 2, 4, 5);
    gtk_table_attach_defaults(GTK_TABLE(table), button_minus, 2, 3, 4, 5);
    gtk_table_attach_defaults(GTK_TABLE(table), button_mul , 3, 4, 2, 3);
    gtk_table_attach_defaults(GTK_TABLE(table), button_div, 3, 4, 3, 4);
    gtk_table_attach_defaults(GTK_TABLE(table), button_equal, 3, 4, 4, 5);
    gtk_table_attach_defaults(GTK_TABLE(table), button_delete, 3, 4, 1, 2);
    // 显示
    gtk_widget_show_all(window);
    // 循环
    gtk_main();
    return 0;
}
```
```shell
gcc -o main main.c `pkg-config --libs -cflags gtk+-3.0`
```
运行结果
![](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/gtk%E5%AE%B9%E5%99%A8%E5%B8%83%E5%B1%80-%E8%AE%A1%E7%AE%97%E5%99%A81.png)

## 信号与回调函数

> GTK采用了信号与回调函数来处理窗口外部传来的事件、消息或信号。当信号发生时，程序自动调用为信号连接的回调函数。

> GTK中的“信号”实际上是一种软件中断。“中断”在我们生活中经常遇到，譬如，我正在房间里打游戏，突然送快递的来了，把正在玩游戏的我给“中断”了，我去签收快递( 处理中断 )，处理完成后，再继续玩我的游戏。GTK中的“信号”就是属于这么一种“中断”，当用户按下按钮的时候，就产生一个“中断”，相当于产生一个信号，接着就会处理这么一个“中断任务”(程序里体验为调用一个函数)。

> “信号”在GTK中可以认为一种中断的标志，如按下按钮的标志为"pressed",释放按钮的标志为"released"，这些标志就相当于 C语言的关键字一样，我们使用的时候必须完全按照它的名字来写。需要注意的是，每个控件的信号标志不一定都一样，如按钮（GtkButton）里有"pressed"信号，窗口(GtkWindow)里就没这个信号，每个控件具体有哪个信号，应该查看帮助文档来确定。

**按钮的常用信号：**

* "clicked" : 单击按钮时触发

* "pressed" : 按下按钮时触发

* "released" : 释放按钮时触发

**信号注册函数**

```c
// instance：信号发出者，可以认为我们操作的控件，如按下按钮，这个就为按钮指针
// detailed_signal：信号标志，如"pressed"
// c_handler：回调函数的名称，需要用G_CALLBACK()进行转换
// data：给回调函数传的参数，gpointer 相当于C语言的 void *
// 返回值：注册函数的标志
gulong g_signal_connect(gpointer instance, const gchar *detailed_signal, GCallback c_handler, gpointer data);
```
例如
```c
g_signal_connect(button, "pressed",G_CALLBACK(callback), NULL);
```

当按下button按钮时，就会自动调用回调函数callback(相当于处理中断任务)，回调函数callback可以是任意函数，函数名字我们根据需要自行命名，如果不是库函数，我们还得定义这个回调函数，这里需要注意的是，回调函数的写法(返回值，参数)，不是我们想怎么写就怎么写，帮助文档里已经规定好了回调函数应该如何写，如果不按规定来写，可能产生意想不到的错误。

[文档地址](https://developer.gnome.org/gtk3/stable/GtkWidget.html)
回调函数的定义
```c
void callback(GtkButton *button,gpointer data) {

}
```
示例
```c
#include <stdio.h>
#include <gtk/gtk.h>

void deal_pressed(GtkButton *button, gpointer user_data) {
    const char *text = gtk_button_get_label(button);
    printf("%s----%s\n", (char *) user_data, text);
}

int main(int argc, char* argv[]) {
    // 初始化
    gtk_init(&argc, &argv);
    // 创建窗口
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    // 设置窗口边框与内部控件的间隔为15
    gtk_container_set_border_width(GTK_CONTAINER(window), 15);
    // 创建一个button
    GtkWidget *button = gtk_button_new_with_label("button1");
    // 将button添加到window中
    gtk_container_add(GTK_CONTAINER(window), button);
    // 创建一个按钮信号
    g_signal_connect(button, "pressed", G_CALLBACK(deal_pressed), "hello, world");
    // 显示控件
    gtk_widget_show_all(window);
    gtk_main();
    return 0;
}
```

### 完成计算器计算功能
```c
#include <stdio.h>
#include <gtk/gtk.h>
#include <string.h>

char buf[50] = "";

void deal_num(GtkButton *button, gpointer data) {
    const char *text = gtk_button_get_label(button);
    // 如果是退格键
    if (0 == strcmp("c", text)) {
        buf[strlen(buf) - 1] = 0;
    } else {
        int a = 0, b = 0;
        char c;
        // 连接buf和新传入的字符串
        strcat(buf, text);
        if (0 == strcmp("=", text)) {
            printf("text=====##%s##\n", text);
            // 从一个字符串中读进与指定格式相符的数据
            sscanf(buf, "%d%c%d", &a, &c, &b);
            printf("---------001-----%c--\n", c);
            if ('+' == c) {
                sprintf(buf, "%d", a + b);
            } else if ('-' == c) {
                sprintf(buf, "%d", a - b);
            } else if ('*' == c) {
                sprintf(buf, "%d", a * b);
            } else if ('/' == c) {
                sprintf(buf, "%d", a / b);
            }
        }
    }
    gtk_entry_set_text(GTK_ENTRY(data), buf);
}


int main(int argc, char* argv[]) {
    // 初始化
    gtk_init(&argc, &argv);
    // 创建窗口
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    // 创建5行4列的表格容器
    GtkWidget *table = gtk_table_new(5, 4, TRUE);
    // 将table放入到容器中
    gtk_container_add(GTK_CONTAINER(window), table);
    // 创建一个行编辑
    GtkWidget *entry = gtk_entry_new();
    // 设置行编辑的内容
    gtk_entry_set_text(GTK_ENTRY(entry), "2+2=4");
    // 设置行编辑不允许编辑
    gtk_editable_set_editable(GTK_EDITABLE(entry), FALSE);
    // 创建0-9数字按钮，+-×/=c功能按钮
    GtkWidget *button_0 = gtk_button_new_with_label("0");
    GtkWidget *button_1 = gtk_button_new_with_label("1");
    GtkWidget *button_2 = gtk_button_new_with_label("2");
    GtkWidget *button_3 = gtk_button_new_with_label("3");
    GtkWidget *button_4 = gtk_button_new_with_label("4");
    GtkWidget *button_5 = gtk_button_new_with_label("5");
    GtkWidget *button_6 = gtk_button_new_with_label("6");
    GtkWidget *button_7 = gtk_button_new_with_label("7");
    GtkWidget *button_8 = gtk_button_new_with_label("8");
    GtkWidget *button_9 = gtk_button_new_with_label("9");
    GtkWidget *button_add = gtk_button_new_with_label("+");
    GtkWidget *button_minus = gtk_button_new_with_label("-");
    GtkWidget *button_mul = gtk_button_new_with_label("*");
    GtkWidget *button_div = gtk_button_new_with_label("/");
    GtkWidget *button_equal = gtk_button_new_with_label("=");
    GtkWidget *button_delete = gtk_button_new_with_label("c");
    // 将table放入表格布局中
    gtk_table_attach_defaults(GTK_TABLE(table), entry, 0, 4, 0, 1);
    gtk_table_attach_defaults(GTK_TABLE(table), button_0, 0, 1, 4, 5);
    gtk_table_attach_defaults(GTK_TABLE(table), button_1, 0, 1, 3, 4);
    gtk_table_attach_defaults(GTK_TABLE(table), button_2, 1, 2, 3, 4);
    gtk_table_attach_defaults(GTK_TABLE(table), button_3, 2, 3, 3, 4);
    gtk_table_attach_defaults(GTK_TABLE(table), button_4, 0, 1, 2, 3);
    gtk_table_attach_defaults(GTK_TABLE(table), button_5, 1, 2, 2, 3);
    gtk_table_attach_defaults(GTK_TABLE(table), button_6, 2, 3, 2, 3);
    gtk_table_attach_defaults(GTK_TABLE(table), button_7, 0, 1, 1, 2);
    gtk_table_attach_defaults(GTK_TABLE(table), button_8, 1, 2, 1, 2);
    gtk_table_attach_defaults(GTK_TABLE(table), button_9, 2, 3, 1, 2);
    gtk_table_attach_defaults(GTK_TABLE(table), button_add, 1, 2, 4, 5);
    gtk_table_attach_defaults(GTK_TABLE(table), button_minus, 2, 3, 4, 5);
    gtk_table_attach_defaults(GTK_TABLE(table), button_mul, 3, 4, 2, 3);
    gtk_table_attach_defaults(GTK_TABLE(table), button_div, 3, 4, 3, 4);
    gtk_table_attach_defaults(GTK_TABLE(table), button_equal, 3, 4, 4, 5);
    gtk_table_attach_defaults(GTK_TABLE(table), button_delete, 3, 4, 1, 2);

    //7.注册信号函数，把entry传给回调函数deal_num()
    g_signal_connect(button_0, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_1, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_2, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_3, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_4, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_5, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_6, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_7, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_8, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_9, "pressed", G_CALLBACK(deal_num), entry);

    g_signal_connect(button_add, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_mul, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_div, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_minus, "pressed", G_CALLBACK(deal_num), entry);
    g_signal_connect(button_equal, "pressed", G_CALLBACK(deal_num), entry);

    // 显示
    gtk_widget_show_all(window);
    // 循环
    gtk_main();
    return 0;
}
```
结果(并不完美)
![](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/gtk-%E8%AE%A1%E7%AE%97%E5%99%A8.gif)

### Glade
> Glade是GTK＋的界面辅助设计工具，可以通过拖放控件的方式快速设计出用户界面，这样的优势在于在设计的同时能直观地看到界面上的控件，并且可以随时调整界面上的设计。

> 用glade设计的图形用户界面是以XML格式的文件保存，它描述了控件的结构、每个控件的属性。用户可以动态加载这个界面文件。而且，界面和程序逻辑是完全分离，用户修改了界面，也不需要重新编译程序。

**安装**
```shell
# ubuntu 
$ sudo apt-get install glade libglade2-dev
```

**Glade文件**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- Generated with glade 3.22.1 -->
<interface>
  <requires lib="gtk+" version="3.20"/>
  <object class="GtkWindow" id="window1">
    <property name="name">window1</property>
    <property name="can_focus">False</property>
    <property name="title" translatable="yes">第一个顶层窗口</property>
    <child type="titlebar">
      <placeholder/>
    </child>
    <child>
      <object class="GtkLayout" id="layout1">
        <property name="name">layout1</property>
        <property name="visible">True</property>
        <property name="can_focus">False</property>
        <child>
          <object class="GtkEntry" id="entry1">
            <property name="name">search_input</property>
            <property name="width_request">200</property>
            <property name="height_request">40</property>
            <property name="visible">True</property>
            <property name="can_focus">True</property>
            <property name="text" translatable="yes">search_input</property>
            <property name="primary_icon_tooltip_text" translatable="yes">search_input</property>
          </object>
          <packing>
            <property name="x">10</property>
            <property name="y">10</property>
          </packing>
        </child>
        <child>
          <object class="GtkButton" id="button1">
            <property name="label" translatable="yes">搜索</property>
            <property name="name">search_button</property>
            <property name="width_request">100</property>
            <property name="height_request">40</property>
            <property name="visible">True</property>
            <property name="can_focus">True</property>
            <property name="receives_default">True</property>
          </object>
          <packing>
            <property name="x">220</property>
            <property name="y">10</property>
          </packing>
        </child>
        <child>
          <object class="GtkButton" id="button2">
            <property name="label" translatable="yes">查看</property>
            <property name="name">show_button</property>
            <property name="width_request">100</property>
            <property name="height_request">40</property>
            <property name="visible">True</property>
            <property name="can_focus">True</property>
            <property name="receives_default">True</property>
          </object>
          <packing>
            <property name="x">350</property>
            <property name="y">10</property>
          </packing>
        </child>
      </object>
    </child>
  </object>
</interface>
```

```c
#include <stdio.h>
#include <gtk/gtk.h>

int main(int argc, char* argv[]) {
    // 初始化
    gtk_init(&argc, &argv);
    // 创建GtkBuilder对象
    GtkBuilder *builder = gtk_builder_new();
    // 读取glade文件
    if(!gtk_builder_add_from_file(builder, "../1.glade", NULL)) {
        printf("无法打开文件");
    }
    // 获取窗口指针
    GtkWidget *window1 = GTK_WIDGET(gtk_builder_get_object(builder, "window1"));

    // 显示
    gtk_widget_show_all(window1);
    // 循环
    gtk_main();
    return 0;
}
```

### 图片控件

图片控件的创建：

```c 
// filename：图片的名字，带路径的话需要加上路径( 相对或绝对 )
GtkWidget *gtk_image_new_from_file( const gchar *filename );
```

通过上面方法创建的图片控件，以图片默认大小来显示，不能修改其大小。如果要改变图片的大小，我们要借助图片资源对象GdkPixbuf，需要注意的是，GdkPixbuf不属于控件类，它以 Gdk 开头。

图片资源对象pixbuf的创建：

```c
// filename：图片的名字，带路径的话需要加上路径( 相对或绝对 )
// error：储存错误的指针
// 返回值：pixbuf指针
GdkPixbuf *gdk_pixbuf_new_from_file(const gchar *filename, GError **error);
```

设置图片的大小：

```c
// src：gdk_pixbuf_new_from_file()的返回值
// dest_width：图片的宽度
// dest_height：图片的高度
// interp_type：是一个枚举变量，标志图片的加载速度和质量，常用GDK_INTERP_BILINEAR
// 返回值：指定大小好的pixbuf指针
GdkPixbuf *gdk_pixbuf_scale_simple(const GdkPixbuf *src, int dest_width, int dest_height, GdkInterpType interp_type);
```

通过gdk_pixbuf_new_from_file(), gdk_pixbuf_scale_simple()创建的pixbuf，它会动态分配空间，占用资源，用完后，需要人为释放资源。

释放资源：

```c
void g_object_unref(GtkObject *object);
```

通过pixbuf来创建图片控件：

```c
// pixbuf：指定好大小的pixbuf
// 返回值：图片控件指针
GtkWidget *gtk_image_new_from_pixbuf(GdkPixbuf *pixbuf );
```

图片控件重新设置一张图片(pixbuf)：

```c
// image：图片控件指针
// pixbuf：指定好大小的pixbuf
void gtk_image_set_from_pixbuf(GtkImage *image, GdkPixbuf *pixbuf );
```

示例代码

```c
#include <stdio.h>
#include <gtk/gtk.h>

int main(int argc, char* argv[]) {
    // 初始化
    gtk_init(&argc, &argv);
    // 创建一个window
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    // 创建水平容器
    GtkWidget *hbox = gtk_box_new(GTK_ORIENTATION_HORIZONTAL, 10);
    // 添加到window
    gtk_container_add(GTK_CONTAINER(window), hbox);
    // 创建图片控件
    GtkWidget *image1 = gtk_image_new_from_file("../img/1.png");
    // 将image添加到box
    gtk_container_add(GTK_CONTAINER(hbox), image1);
    // 创建pixbuf
    GdkPixbuf *src = gdk_pixbuf_new_from_file("../img/1.png", NULL);
    // 修改图片大小
    GdkPixbuf *dst = gdk_pixbuf_scale_simple(src, 100, 100, GDK_INTERP_BILINEAR);
    GtkWidget *image2 = gtk_image_new_from_pixbuf(dst);
    // 释放pxibuf资源
    g_object_unref(src);
    g_object_unref(dst);
    // 将image2添加到box
    gtk_container_add(GTK_CONTAINER(hbox), image2);
    // 获取控件里的pixbuf
    GdkPixbuf *tmp = gtk_image_get_pixbuf(GTK_IMAGE(image2));
    GtkWidget *image3 = gtk_image_new_from_pixbuf(tmp);
    g_object_unref(tmp);
    gtk_container_add(GTK_CONTAINER(hbox), image3);
    // 给创建的图片控件重新设置图片
    GtkWidget *image4 = gtk_image_new_from_file("../img/1.png");
    gtk_container_add(GTK_CONTAINER(hbox), image4);
    src = gdk_pixbuf_new_from_file("../img/2.png", NULL);
    dst = gdk_pixbuf_scale_simple(src, 200, 200, GDK_INTERP_BILINEAR);
    gtk_image_set_from_pixbuf(GTK_IMAGE(image4), dst);
    g_object_unref(src);
    g_object_unref(dst);
    // 显示所有控件
    gtk_widget_show_all(window);
    // 循环
    gtk_main();
    return 0;
}
```

### 进度条控件

[文档](https://developer.gnome.org/gtk3/stable/GtkProgressBar.html#gtk-progress-bar-get-fraction)

进度条的创建

```c
// 返回值：进度条指针
GtkWidget *gtk_progress_bar_new(void);
```
设置进度条显示的进度比例

```c
// pbar：需要操作的进度条
// fraction：0.0到1.0
void gtk_progress_bar_set_fraction(GtkProgressBar *pbar, gdouble fraction);
```

获取进度条显示的进度比例

```c
// pbar：需要操作的进度条
// 返回值：进度比例
gdouble  gtk_progress_bar_get_fraction (GtkProgressBar *pbar);
```

设置滑槽上的文本显示

```c
// pbar：需要操作的进度条
// text：设置的文本内容
void gtk_progress_bar_set_text(GtkProgressBar *pbar, gchar *text);
```

设置进度条的移动方向gtk+-2版本

```c
// pbar：需要操作的进度条
// orientation：移动方向，它是一个枚举变量
// GTK_PROGRESS_LEFT_TO_RIGHT：从左向右 
// GTK_PROGRESS_RIGHT_TO_LEFT：从右向左 
// GTK_PROGRESS_BOTTOM_TO_TOP：从下向上 
// GTK_PROGRESS_TOP_TO_BOTTOM：从上向下
void gtk_progress_bar_set_orientation(GtkProgressBar *pbar, GtkProgressBarOrientation orientation);
```

获取进度条的方向gtk+-2版本

```c
// pbar：需要操作的进度条
// 返回值：进度条的方向，它是一个枚举类型
GtkProgressBarOrientationgtk_progress_bar_get_orientation(GtkProgressBar *pbar);
```

设置进度条的移动方向gtk+-3版本

```c
void gtk_progress_bar_set_inverted (GtkProgressBar *pbar, gboolean inverted);
```

```c
gboolean gtk_progress_bar_get_inverted (GtkProgressBar *pbar);
```

```c
#include <gtk/gtk.h>


//回调函数，切换进度条的移动方向
void toggle_orientation(GtkWidget *widget, gpointer data)
{
    switch (gtk_progress_bar_get_inverted(GTK_PROGRESS_BAR(data)) ) {
        case TRUE:
            gtk_progress_bar_set_inverted(GTK_PROGRESS_BAR(data), FALSE);
            break;
        case FALSE:
            gtk_progress_bar_set_inverted(GTK_PROGRESS_BAR(data), TRUE);
            break;
        default:
            break;
    }
}


//回调函数，更新进度条，这样可以看到进度条的移动
void callback(GtkWidget *widget, gpointer data)
{
    gdouble new_val = gtk_progress_bar_get_fraction(GTK_PROGRESS_BAR(data)) +0.05;

    if (new_val > 1.0) {
        new_val = 0.0;
    }

    //设置进度条新值
    gtk_progress_bar_set_fraction(GTK_PROGRESS_BAR(data), new_val);

}

int main(int argc, char *argv[])
{
    //1.gtk环境的初始化
    gtk_init(&argc, &argv);

    //2.创建一个窗口
    GtkWidget *window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    //设置窗口的标题
    gtk_window_set_title(GTK_WINDOW(window), "gtk_progress_bar");
    //设置窗口边框宽度
    gtk_container_set_border_width(GTK_CONTAINER(window), 10);

    //3.设置窗口的最小大小
    gtk_widget_set_size_request(window, 300, 200);

    //4.窗口关联destroy信号到gtk_main_quit
    g_signal_connect(window, "destroy", G_CALLBACK(gtk_main_quit), NULL);

    //5.创建一个垂直容器
    GtkWidget *vbox = gtk_box_new(GTK_ORIENTATION_VERTICAL, 5);
    //将vbox添加到window中
    gtk_container_add(GTK_CONTAINER(window), vbox);

    //6.创建一个进度条
    GtkWidget *progress = gtk_progress_bar_new();
    //将progress添加到vbox中
    gtk_container_add(GTK_CONTAINER(vbox), progress);

    //7.设置进度条的百分比为50%
    gtk_progress_bar_set_fraction(GTK_PROGRESS_BAR(progress), 0.5);

    //8.设置进度条上显示的文字
    gtk_progress_bar_set_text(GTK_PROGRESS_BAR(progress), "some text");

    //9.添加一个button1按钮,切换方向
    GtkWidget *button1 = gtk_button_new_with_label("right to lift");
    gtk_container_add(GTK_CONTAINER(vbox), button1);
    g_signal_connect(button1, "clicked", G_CALLBACK(    toggle_orientation   ), progress);

    //10.添加一个button2，移动进度条
    GtkWidget *button2 = gtk_button_new_with_label("add");
    gtk_container_add(GTK_CONTAINER(vbox), button2);
    g_signal_connect(button2, "clicked", G_CALLBACK(callback), progress);

    //11.显示所欲窗口
    gtk_widget_show_all(window);

    //12.主事件循环
    gtk_main();

    return 0;
}

```












