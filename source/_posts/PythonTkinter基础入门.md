---
title: PythonTkinter基础入门
tags:
  - python
url: 99.html
id: 99
comments: false
categories:
  - 后端
date: 2016-11-10 10:58:02
---

python基础环境

    sudo apt-get install python
    sudo apt-get install python-pip
    

Tkinter安装

    sudo apt-get install python-tk
    

配置组件外观

    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    root = Tk()
    # 定义图标
    if sys.platform == 'win32':
        root.iconbitmap('favicon.ico')
    else:
        icon = PhotoImage(file='phone.gif')
        root.tk.call('wm', 'iconphoto', root._w, icon)
    # 定义字体，字号，类型
    label_font = ('Ubuntu', 20, 'bold underline italic')
    # 创建组件
    widget = Label(root, text='Hello,云克隆', padx=100, pady=20)
    # 配置组件字体
    widget.config(font=label_font)
    # 组件宽，高
    widget.config(height=3, width=20)
    # 组件背景颜色
    widget.config(bg='#FF0')
    # 组件字体颜色
    widget.config(fg='#F0F')
    # 组件边框宽度
    widget.config(bd=3)
    # 组件边框外形, FLAT扁平, SUNKEN凹陷, RAISED凸起, GROOVE凹槽, SOLID加粗, RIDGE脊状,
    widget.config(relief=SUNKEN)
    # 组件鼠标手势, 'gumby', 'watch', 'pencil', 'crdss', hand2
    widget.config(cursor='gumby')
    # 组件状态, DISABLE禁用, NORMAL正常, READONLY只读
    widget.config(state=DISABLED)
    # 布置组件
    # expand设置是否可以扩展，fill表示随父组件扩展方向，可以设置BOTH, X, Y
    # 设置组件padding值
    # side表示按钮布置窗口的位置
    widget.pack(expand=True, fill=X, padx=10, pady=50, side=LEFT)
    root.mainloop()
    

#### 标准弹出框

    # -*- coding: UTF-8 -*-
    from Tkinter import *
    from tkMessageBox import *
    # python3
    # from Tkinter.messagebox import *
    def callback():
        if askyesno('Verify', 'Do you ready want to quit?'):
            showwarning('Yes', 'Quit not yet')
        else:
            showinfo('No', 'Quit has been cancelled')
    
    errmsg = 'Sorry, no spam allowed'
    Button(None, text='Quit', command=callback).pack(fill=X)
    Button(None, text='spam', command=(lambda: showerror('spam', errmsg))).pack(fill=X)
    mainloop()
    

#### 常见弹框

    # gui6.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    from tkMessageBox import *
    from tkFileDialog import *
    from tkColorChooser import *
    from tkSimpleDialog import *
    
    
    demos = {
        'Open': askopenfilename,
        'Color': askcolor,
        'Query': lambda: askquestion('Warning', 'You typed "rm *"\nconfirm?'),
        'Error': lambda: showerror('Error!', 'He is dead'),
        'Input': lambda: askfloat('Entry', 'Enter credit card number')
    }
    
    
    class Demo(Frame):
        def __init__(self, parent=None, **options):
            Frame.__init__(self, parent, **options)
            self.pack()
            Label(self, text='Basic demos').pack()
            for (key, value) in demos.items():
                Button(self, text=key, command=value).pack(side=TOP, fill=BOTH)
    
    if __name__ == '__main__':
        Demo().mainloop()
    自定义弹框
    
    # -*- coding: UTF-8 -*-
    import sys
    from Tkinter import *
    makemodal = (len(sys.argv) > 1)
    
    def dialog():
        win = Toplevel()
        Label(win, text='Hard drive reformatted!').pack()
        Button(win, text='OK', command=win.destroy).pack()
        if makemodal:
            # 获取输入的焦点
            win.focus_set()
            # 打开的时候，禁用其他窗口
            win.grab_set()
            # 在win销毁之前，继续等待
            win.wait_window()
        print 'dialog exit'
    
    root = Tk()
    Button(root, text='popup', command=dialog).pack()
    root.mainloop()
    

#### 绑定事件

    # -*- coding: UTF-8 -*-
    from Tkinter import *
    
    
    def showPosEvent(event):
        print('Widget=%s, X=%s, Y=%s' % (event.widget, event.x, event.y))
    
    
    def showAllEvent(event):
        print(event)
        for attr in dir(event):
            if not attr.startswith('__'):
                print(attr, '=>', getattr(event, attr))
    
    
    def onKeyPress(event):
        print('Got key press: ', event.char)
    
    
    def onArrowKey(event):
        print('Got up arrow key press')
    
    
    def onReturnKey(event):
        print('Got return key press')
    
    
    def onLeftClick(event):
        print('Got left mouse button click: ')
        showPosEvent(event)
    
    
    def onRightClick(event):
        print('Got right mouse button click: ')
        showPosEvent(event)
    
    
    def onMiddleClick(event):
        print('Got middle mouse button click: ')
        showPosEvent(event)
        showAllEvent(event)
    
    
    def onLeftDrag(event):
        print('Got left mouse drag click: ')
        showPosEvent(event)
    
    
    def onDoubleLeftClick(event):
        print('Got double left mouse click: ')
        showPosEvent(event)
        tkroot.quit()
    
    
    tkroot = Tk()
    labelfont = ('courier', 20, 'bold')
    widget = Label(tkroot, text='Hello bind world')
    widget.config(bg='red', font=labelfont)
    widget.config(height=5, width=20)
    widget.pack(expand=YES, fill=BOTH)
    # 绑定单击左键
    widget.bind('<Button-1>', onLeftClick)
    # 绑定单击右键
    widget.bind('<Button-3>', onRightClick)
    # 绑定单击中键
    widget.bind('<Button-2>', onMiddleClick)
    # 绑定双击左键
    widget.bind('<Double-1>', onDoubleLeftClick)
    # 绑定拖拽 B1表示点击左键，Motion表示拖拽
    widget.bind('<B1-Motion>', onLeftDrag)
    # 绑定键盘按键
    widget.bind('<KeyPress>', onKeyPress)
    # 绑定按下箭头键
    widget.bind('<Up>', onArrowKey)
    # 绑定回车，返回键
    widget.bind('<Return>', onReturnKey)
    widget.focus()
    tkroot.title('CLICK ME')
    tkroot.mainloop()
    
    # <ButtonPress> 按下按钮
    # <ButtonRelease> 松开按钮
    # <Motion> 鼠标指针移动
    # <Configure> 窗口大小，位置改变时候
    # <Destroy> 销毁窗口
    # <FocusIn> 获取焦点
    # <FocusOut> 失去焦点
    # <Map> 窗口打开
    # <Unmap> 窗口最小化
    # <Escape>, <BackSpace>, <Tab> 捕获特点ESC键，回车键，tab键
    # <Down>, <Up>, <Left>, <Right> 捕获方向键
    # <B1-Motion> 按下左键并拖拽
    # <KeyPress-a> 按下按键“a”，大小写敏感
    

#### 输入框

    # -*- coding: UTF-8 -*-
    from Tkinter import *
    from quitGui import Quitter
    
    fields = 'Name', 'Job', 'Pay'
    
    
    def fetch(entries):
        for entry in entries:
            print('Input => %s' % entry.get())  # 获取文本信息
    
    
    def makeform(root, fields):
        entries = []
        for field in fields:
            row = Frame(root)
            lab = Label(row, width=5, text=field)
            ent = Entry(row)
            row.pack(side=TOP, fill=X)
            lab.pack(side=LEFT)
            ent.pack(side=RIGHT, expand=YES, fill=X)
            entries.append(ent)
        return entries
    
    
    def show(entries, popup):
        fetch(entries)  # 必须在销毁前取出
        popup.destroy()  # 如果修改了stmt， order， msgs会随之失败
    
    
    def ask():
        popup = Toplevel()  # 在模拟对话框窗口中显示表单
        ents = makeform(popup, fields)
        Button(popup, text='OK', command=(lambda: show(ents, popup))).pack()
        popup.grab_set()  # 打开的时候，禁用其他窗口
        popup.focus_set()  # 获取输入的焦点
        popup.wait_window()  # 在这里等待销毁
    
    
    if __name__ == '__main__':
        root = Tk()
        Button(root, text='Dialog', command=ask).pack()
        root.mainloop()
    

#### 复选框

    # gui13.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    from gui6 import demos
    from quitGui import Quitter
    
    
    class Demo(Frame):
        def __init__(self, parent=None, **options):
            Frame.__init__(self, parent, **options)
            self.pack()
            self.tools()
            Label(self, text='Ckech demos').pack()
            self.vars = []
            for key in demos:
                var = IntVar()
                Checkbutton(self, text=key, variable=var, command=demos[key]).pack(side=LEFT)
                self.vars.append(var)
    
        def report(self):
            for var in self.vars:
                print(var.get())  # 当前开关设置1或者0
            print()
    
        def tools(self):
            frm = Frame(self)
            frm.pack(side=RIGHT)
            Button(frm, text='State', command=self.report).pack(fill=X)
            Quitter(frm).pack(fill=X)
    
    
    if __name__ == '__main__':
        Demo().mainloop()
    
    

#### 单选按钮

    #gui14.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    from gui6 import demos
    from quitGui import Quitter
    
    
    class Demo(Frame):
        def __init__(self, parent=None, **options):
            Frame.__init__(self, parent, **options)
            self.pack()
            Label(self, text='Radio demos').pack(side=TOP)
            self.var = StringVar()
            # variable表示单选按钮的变量，相当与html的name标签
            # value表示单选按钮的变量值，相当与html的value标签
            for key in demos:
                Radiobutton(self, text=key, command=self.onPress, variable=self.var, value=key).pack(anchor=NW)
            self.var.set(key)  # 选中最后一个开始
            Button(self, text='State', command=self.report).pack(fill=X)
            Quitter(self).pack(fill=X)
    
        def onPress(self):
            pick = self.var.get()
            print('you pressed', pick)
            print('result: ', demos[pick]())  # 按钮使用返回的值
    
        def report(self):
            print(self.var.get())
    
    if __name__ == '__main__':
        Demo().mainloop()
    

#### 标尺滚动条

    # gui16.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    from gui6 import demos
    from quitGui import Quitter
    
    
    class Demo(Frame):
        def __init__(self, parent=None, **options):
            Frame.__init__(self, parent, **options)
            self.pack()
            Label(self, text='Scale demos').pack()
            self.var = IntVar()
            Scale(self, label='Pick demo number',
                  command=self.onMove, variable=self.var,
                  from_=0, to=len(demos)-1).pack()
            Scale(self, label='Pick demo number',  # 标尺的文本
                  command=self.onMove,  # 绑定回调
                  variable=self.var,  # 变量
                  from_=0,   # 标尺最小值
                  to=len(demos) - 1,   # 标尺最大值
                  length = 200,  # 长度
                  tickinterval = 2,   # 每隔几个单位显示标记单位
                  showvalue = YES,  # 显示或隐藏标尺滑动条旁边的当前值
                  orient = 'horizontal'  # 横向展示
                  ).pack()
            Quitter(self).pack(side=RIGHT)
            Button(self, text='Run demo', command=self.onRun).pack(side=LEFT)
            Button(self, text='State', command=self.report).pack(side=RIGHT)
    
        def onMove(self, value):
            print('in onMove', value)
    
        def onRun(self):
            pos = self.var.get()
            print('you picked', pos)
            demo = list(demos.values())[pos]
            print(demo())
    
        def report(self):
            print(self.var.get())
    
    if __name__ == '__main__':
        print(list(demos.keys()))
        Demo().mainloop()
    

#### 创建图片

    # gui17.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    
    gifdir = './gif/'
    win = Tk()
    igm = PhotoImage(file=gifdir + 'logo.png')
    
    # 按钮上绑定图片
    # Button(win, image=igm).pack()
    # win.mainloop()
    
    # 创建画布
    can = Canvas(win)
    can.pack(fill=BOTH)
    can.config(width=igm.width() + 4, height=igm.height() + 4)
    can.create_image(2,  # x坐标
                     2,  # y坐标
                     image=igm, anchor=NW)
    win.mainloop()
    

### 按钮随机读取图片

    # gui18.py
    # -*- coding: UTF-8 -*-
    from glob import glob
    from gui13 import *
    import random
    
    gifdir = './gif/'
    
    " 按钮随机读取图片 "
    class ButtonPicsDemo(Frame):
        def __init__(self, gifdir=gifdir, parent=None):
            Frame.__init__(self, parent)
            self.pack()
            self.lbl = Label(self, text='none', bg='blue', fg='red')
            self.pix = Button(self, text='press me', command=self.draw, bg='white')
            self.lbl.pack(fill=BOTH)
            self.pix.pack(pady=10)
            Demo(self, relief=SUNKEN, bd=2).pack(fill=BOTH)
            # glob模块， glob方法，返回所有匹配的文件列表
            files = glob(gifdir + '*.png')
            self.images = [(x, PhotoImage(file=x)) for x in files]
            print(files)
    
        def draw(self):
            name, photo = random.choice(self.images)
            self.lbl.config(text=name)
            self.pix.config(image=photo)
    
    if __name__ == '__main__':
        if __name__ == '__main__':
            ButtonPicsDemo().mainloop()
    

#### 缩略图

    # gui19.py
    # -*- coding: UTF-8 -*-
    import os
    import math
    import PIL
    from PIL import Image
    from Tkinter import *
    
    
    def make_thumbs(img_dir, size=(100, 100), subdir='thumbs'):
        thumb_dir = os.path.join(img_dir, subdir)
        if not os.path.exists(thumb_dir):
            os.mkdir(thumb_dir)
        thumbs = []
        for img_file in os.listdir(img_dir):
            thumb_path = os.path.join(thumb_dir, img_file)
            if os.path.exists(thumb_path):
                thumb_obj = PIL.Image.open(thumb_path)
                thumbs.append((img_file, thumb_obj))
            else:
                print('making', thumb_path)
                img_path = os.path.join(img_dir, img_file)
                try:
                    img_obj = PIL.Image.open(img_path)
                    img_obj.thumbnail(size, PIL.Image.ANTIALIAS)
                    img_obj.save(thumb_path)
                    thumbs.append((img_file, img_obj))
                except Exception, e:
                    print('skipping: ', img_path)
                    print e
        return thumbs
    
    
    class ViewOne(Toplevel):
        def __init__(self, img_dir, img_file):
            Toplevel.__init__(self)
            self.title(img_file)
            img_path = os.path.join(img_dir, img_file)
            img_obj = PhotoImage(file=img_path)
            Label(self, image=img_obj).pack()
            print(img_path, img_obj.width(), img_obj.height())
            self.savephoto = img_obj
    
    
    def viewer(img_dir, kind=Toplevel, cols=None):
        win = kind()
        win.title('VIEWER: ' + img_dir)
        thumbs = make_thumbs(img_dir)
        if not cols:
            # 固定大小 或者N×N
            cols = int(math.ceil(math.sqrt(len(thumbs))))
        savephotos = []
        while thumbs:
            thumbs_row, thumbs = thumbs[:cols], thumbs[cols:]
            row = Frame(win)
            row.pack(fill=BOTH)
            for(img_file, img_obj) in thumbs_row:
                size = max(img_obj.size)
                photo = PhotoImage(file=os.path.join(img_dir, 'thumbs', img_file))
                link = Button(row, image=photo)
            handler = lambda savefile=img_file: ViewOne(img_dir, savefile)
            link.config(command=handler, width=size, height=size)
            link.pack(side=LEFT, expand=YES)
            savephotos.append(photo)
        Button(win, text='Quit', command=win.quit, bg='beige').pack(fill=X)
        return win, savephotos
    
    if __name__ == '__main__':
        img_dir = len(sys.argv) > 1 and sys.argv[1] or 'gif'
        main, save = viewer(img_dir, kind=Tk)
        main.mainloop()
    

### 创建基础顶级菜单

    # gui20.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    from tkMessageBox import *
    
    
    def not_done():
        showerror('Not implemented', 'Not yet available')
    
    
    def make_menu(win):
        top = Menu(win)  # 定义顶级菜单
        win.config(menu=top)
        file = Menu(top, tearoff=True)  # 一级菜单 tearoff:设置菜单下的虚线是否显示
        file.add_command(label='New...', command=not_done, underline=0)
        file.add_command(label='Open...', command=not_done, underline=0)
        file.add_command(label='Quit...', command=win.quit,  underline=0)
        top.add_cascade(label='File', menu=file, underline=0)  # 在顶级菜单中添加一级菜单
    
        edit = Menu(top, tearoff=False)  # 一级菜单
        edit.add_command(label='Cut', command=not_done, underline=0)
        edit.add_command(label='Paste', command=not_done, underline=0)
        edit.add_separator()  # 添加下划线
        top.add_cascade(label='Edit', menu=edit, underline=0)
    
        submenu = Menu(edit, tearoff=True)  # 二级菜单
        submenu.add_command(label='Spam', command=win.quit, underline=0)
        submenu.add_command(label='Eggs', command=not_done, underline=0)
        edit.add_cascade(label='Stuff', menu=submenu, underline=0)  # 在一级菜单中添加二级菜单
    
    if __name__ == '__main__':
        root = Tk()
        root.title('menu_win')
        make_menu(root)
        msg = Label(root, text='Window menu basic')
        msg.pack(expand=YES, fill=BOTH)
        msg.config(relief=SUNKEN, width=40, height=7, bg='beige')
        root.mainloop()
    

#### 创建框架级别菜单

    # gui21.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    from tkMessageBox import *
    
    
    def not_done():
        showerror('Not implemented', 'Not yet available')
    
    
    def make_menu(parent):
        menu_bar = Frame(parent)
        menu_bar.pack(side=TOP, fill=X)
    
        f_button = Menubutton(menu_bar, text='File', underline=0)
        f_button.pack(side=LEFT)
        file = Menu(f_button)
        file.add_command(label='New...', command=not_done, underline=0)
        file.add_command(label='Open...', command=not_done, underline=0)
        file.add_command(label='Quit...', command=parent.quit,  underline=0)
        f_button.config(menu=file)
    
        e_button = Menubutton(menu_bar, text='Edit', underline=0)
        e_button.pack(side=LEFT)
        edit = Menu(e_button)
        edit.add_command(label='Cut', command=not_done, underline=0)
        edit.add_command(label='Paste', command=not_done, underline=0)
        edit.add_separator()
        e_button.config(menu=edit)
    
        sub_menu = Menu(edit, tearoff=True)
        sub_menu.add_command(label='Spam', command=parent.quit, underline=0)
        sub_menu.add_command(label='Eggs', command=not_done, underline=0)
        edit.add_cascade(label='Stuff', menu=sub_menu, underline=0)
        return menu_bar
    
    
    
    if __name__ == '__main__':
        root = Tk()
        root.title('menu_fra')
        for i in range(2):
            mnu = make_menu(root)
            mnu.config(bd=2, relief=RAISED)
            Label(root, bg='black', height=5, width=25).pack(expand=YES, fill=BOTH)
        Button(root, text='Bye', command=root.quit).pack()
        root.mainloop()
    

#### 图片菜单

    # gui22.py
    # -*- coding:UTF-8 -*-
    from Tkinter import *
    from tkMessageBox import *
    
    
    class NewMenuDemo(Frame):
        def __init__(self, parent=None):
            Frame.__init__(self, parent)
            self.pack(expand=YES, fill=BOTH)
            self.create_widgets()
    
        def create_widgets(self):
            self.make_menubar()
            self.make_toolbar()
            l = Label(self, text='Menu and Toolbar Demo')
            l.config(relief=SUNKEN, width=40, height=10, bg='white')
            l.pack(expand=YES, fill=BOTH)
    
        def make_toolbar(self, size=(30, 30)):
            toolbar = Frame(self, cursor='hand2', relief=SUNKEN, bd=2)
            toolbar.pack(side=BOTTOM, fill=X)
            Button(toolbar, text='Quit', command=self.quit).pack(side=RIGHT)
            photo_files = ('at.png', 'bd.png', 'dk.png')
            self.tool_photo_objs = []
            for file in photo_files:
                img = PhotoImage(file='./gif/thumbs/' + file)
                btn = Button(toolbar, image=img, command=self.greeting)
                btn.config(bd=5, relief=RIDGE)
                btn.config(width=size[0], height=size[0])
                btn.pack(side=LEFT)
                self.tool_photo_objs.append(img)
            Button(toolbar, text='Hello', command=self.greeting).pack(side=LEFT)
    
        def make_menubar(self):
            self.menubar = Menu(self.master)
            self.master.config(menu=self.menubar)
            self.file_menu()
            self.edit_menu()
            self.image_menu()
    
        def file_menu(self):
            pulldown = Menu(self.menubar)
            pulldown.add_command(label='OPEN...', command=self.notdone)
            pulldown.add_command(label='QUIT', command=self.quit)
            self.menubar.add_cascade(label='FILE', underline=0, menu=pulldown)
    
        def edit_menu(self):
            pulldown = Menu(self.menubar)
            pulldown.add_command(label='PASTE', command=self.notdone)
            pulldown.add_command(label='SPAM', command=self.greeting)
            pulldown.add_separator()
            pulldown.add_command(label='DELETE', command=self.greeting)
            pulldown.entryconfig(4, state=DISABLED)
            self.menubar.add_cascade(label='EDIT', underline=0, menu=pulldown)
    
        def image_menu(self):
            photo_files = ('at.png', 'bd.png', 'dk.png')
            pulldown = Menu(self.menubar)
            self.photo_objs = []
            for file in photo_files:
                img = PhotoImage(file='./gif/' + file)
                pulldown.add_command(image=img, command=self.notdone)
                self.photo_objs.append(img)
            self.menubar.add_cascade(label='IMAGE', underline=0, menu=pulldown)
    
        def greeting(self):
            showinfo('greeting', 'GREETING')
    
        def notdone(self):
            showerror('NOT DONE', 'NOT DONE')
    
        def quit(self):
            if askyesno('Verify quit', 'Sure?'):
                Frame.quit(self)
    
    if __name__ == '__main__':
        NewMenuDemo().mainloop()
    

#### 滚动条与列表

    # gui23.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    
    
    class ScrolledList(Frame):
        def __init__(self, options, parent=None):
            Frame.__init__(self, parent)
            self.pack(expand=YES, fill=BOTH)
            self.makeWidgets(options)
    
    
        def handle_list(self, event):
            index = self.listbox.curselection()  # 鼠标选中的行
            label = self.listbox.get(index)  # 获取鼠标选中的值
            self.runCommand(label)  # 打印出值
    
    
        def makeWidgets(self, options):
            # sbar = Scrollbar(self, orient='horizontal')  # 横向滚动
            sbar = Scrollbar(self)
            list = Listbox(self, relief=SUNKEN)
            sbar.config(command=list.yview)  # 移动滚动条启用回调： 按比例调节列表框的显示，
            list.config(yscrollcommand=sbar.set)  # 移动列表框启用回调：按比例调节滚动条
            sbar.pack(side=RIGHT, fill=Y)
            list.pack(side=LEFT, expand=YES, fill=BOTH)
            pos = 0
            for label in options:
                list.insert(pos, label)
                pos += 1
            # selectmode 选择模式SINGLE(单选), BROWSE(默认单选), MULTIPLE(多选), EXTENDED(多选)
            # 启用多选时， curselection返回列表
            # 启用单选时， curselection返回字符串
            list.config(selectmode=EXTENDED, setgrid=1)
            list.bind('<Double-1>', self.handle_list)
            self.listbox = list
    
        def runCommand(self, selection):
            print('YOU SELECTED:', selection)
    
    
    if __name__ == '__main__':
        options = (('Lumberjack-%s' % x) for x in range(20))
        ScrolledList(options).mainloop()
    

#### TEXT组件

    gui24.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    
    
    class ScrollText(Frame):
        def __init__(self, parent=None, text='', file=None):
            Frame.__init__(self, parent)
            self.pack(expand=YES, fill=BOTH)
            self.make_widgets()
            self.set_text(text, file)
    
        def make_widgets(self):
            sbar = Scrollbar(self)
            text = Text(self, relief=SUNKEN)
            sbar.config(command=text.yview)
            text.config(yscrollcommand=sbar.set)
            sbar.pack(side=RIGHT, fill=Y)
            text.pack(side=LEFT, expand=YES, fill=BOTH)
            self.text = text
    
        def set_text(self, text='', file=None):
            if file:
                text = open(file, 'r').read()
            # 1.0表示第一行，第0列（行从1开始，列从0开始）
            # END表示最末一个字符后面第一个位置
            self.text.delete('1.0', END)  # 删除从第一行第0列开始到最后的字符串，即删除全部
            self.text.insert('1.0', text)  # 从第一行第0列开始填充数据
            # mark_set 设置text标志
            self.text.mark_set(INSERT, '1.0')  # 设置将光标设置在文本开始位置
            # self.text.mark_set('line_two', '2.2')  # 自定义标志位置 可以用mark_unset取消
            self.text.focus()
            # TEXT的tag标签
            # SEL是内置的标签，表示当前选中的文本
            # text = self.text.get(SEL_FIRST, SEL_LAST)
            # text = self.text.get('sel.first', 'sel.last')
            # self.text.tag_add('alltext', '1.0', END)  # 选中所有文本
            # self.text.tag_add(SEL, index1, index2)  # 将一定范围内的文本添加到内建的SEL标签
            # self.text.tag_remove(SEL, '1.0', END)  # 将字符串所有字符从SEL标签中删除
            # self.text.tag_delete(SEL)  # 删除SEL标签
            # self.text.tag_add('demo', '1.5', '1.7')
            # self.text.tag_add('demo', '3.0', '5.7')
            # self.text.tag_config('demo', background='purple')
            # self.text.tag_config('demo', fontground='white')
            # self.text.tag_config('demo', font=('times', 16, 'underline'))
            # self.text.tag_bind('demo', '<Double-1>', func)
            # see方法
            # self.text.see('1.0')  # 滚动到第一行第0列
            # self.text.see(INSERT)  # 滚动到光标插入处
            # self.text.see(SEL_FIRST)  # 滚动到选中文本第一个字符串
        def get_text(self):
            # -1c 表示往回数一个字符串
            # +1l 表示往前数一行
            return self.text.get('1.0', END + '-1c')
    
    
    if __name__ == '__main__':
        root = Tk()
        if len(sys.argv) > 1:
            st = ScrollText(file=sys.argv[1])
        else:
            st = ScrollText(text='Words\ngo here')
    
        def show(event):
            print(repr(st.get_text()))
    
        root.bind('<Key-Escape>', show)
        root.mainloop()
    

#### 简单的文本编辑器

    # gui25.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    from tkSimpleDialog import askstring
    from tkFileDialog import asksaveasfilename
    from quitGui import Quitter
    from gui24 import ScrollText
    
    
    class SimpleEditor(ScrollText):
        def __init__(self, parent=None, file=None):
            frm = Frame(parent)
            frm.pack(fill=X)
            Button(frm, text='Save', command=self.on_save).pack(side=LEFT)
            Button(frm, text='Cut', command=self.on_cut).pack(side=LEFT)
            Button(frm, text='Paste', command=self.on_paste).pack(side=LEFT)
            Button(frm, text='Find', command=self.on_find).pack(side=LEFT)
            Quitter(frm).pack(side=LEFT)
            ScrollText.__init__(self, parent, file=file)
            self.text.config(font=('courier', 9, 'normal'))
    
        def on_save(self):
            filename = asksaveasfilename()
            if filename:
                alltext = self.get_text()
                open(filename, 'w').write(alltext)
    
    
        def on_cut(self):
            text = self.text.get(SEL_FIRST, SEL_LAST)
            self.text.delete(SEL_FIRST, SEL_LAST)
            self.clipboard_clear()
            self.clipboard_append(text)
    
    
        def on_paste(self):
            try:
                text = self.selection_get(selection='CLIPBOARD')
                self.text.insert(INSERT, text)
            except TclError:
                pass
    
    
        def on_find(self):
            target = askstring('SimpleEdit', 'Search string?')
            if target:
                where = self.text.search(target, INSERT, END)
                if where:
                    print(where)
                    pastit = where + ('+%dc' % len(target))
                    self.text.tag_remove(SEL, '1.0', END)
                    self.text.tag_add(SEL, where, pastit)
                    self.text.mark_set(INSERT, pastit)
                    self.text.see(INSERT)
                    self.text.focus()
    
    if __name__ == '__main__':
        if len(sys.argv) > 1:
            SimpleEditor(file=sys.argv[1]).mainloop()
        else:
            SimpleEditor().mainloop()
    

#### 创建基础画布

    # gui26.py
    # -*- coding: UTF-8 -*-
    
    from Tkinter import *
    canvas = Canvas(width=525, height=300, bg='white')
    canvas.pack(expand=YES, fill=BOTH)
    
    canvas.create_line(100, 100, 200, 200)
    canvas.create_line(100, 200, 200, 300)
    for i in range(1, 20, 2):
        canvas.create_line(0, i, 50, i)
    canvas.create_oval(20, 20, 300, 100, width=10, fill='yellow')
    d = {1:PIESLICE,2:CHORD,3:ARC}
    for i in d:
        canvas.create_arc(
            (10,10 + 60*i,110,110 + 60*i),
            style=d[i],   #指定样式
            start=30,     #指定起始角度
            extent=30     #指定角度偏移量
            )
    canvas.create_arc(0, 0, 100, 200, fill='black')
    canvas.create_rectangle(200, 200, 300, 300, width=5, fill='red')
    canvas.create_line(0, 300, 150, 150, width=10, fill='green')
    
    photo = PhotoImage(file='./gif/lightmachine.png')
    canvas.create_image(325,25, image=photo, anchor=NW)
    
    widget = Label(canvas, text='Spam', fg='white', bg='black')
    widget.pack()
    canvas.create_window(100, 100, window=widget)
    canvas.create_text(100, 280, text='CLOUD CLONE CORP.')
    
    # id = canvas.create_line(fromX, fromY, toX, toY)  # 创建直线
    # id = canvas.create_oval(fromX, fromY, toX, toY) # 创建椭圆
    # id = canvas.create_arc(fromX, fromY, toX, toY)  # 创建扇形
    # id =canvas.create_rectangle(fromX, fromY, toX, toY)  # 创建矩形
    # canvas.move(id, offsetX, offsetY)  # 移动对象
    # canvas.delete(id)  # 删除对象
    # canvas.tkraise(id)  # 将对象移动到前端
    # canvas.lower(id)  # 将对象移动到底部
    # canvas.itemconfig(id, fill='red')  # 修改对象属性
    
    # 画布标签
    # canvas.create_line(fromX, fromY, toX, toY, tag='bubbles') 
    # canvas.create_line(fromX, fromY, toX, toY, tag='bubbles') 
    # id = canvas.create_line(fromX, fromY, toX, toY)
    # canvas.addtag_withtag('bubbles', id)
    # canvas.move('bubbles', diffX, diffY)
    mainloop()
    

#### canvas与滚动条

    # gui27.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    
    class ScrolledCanvas(Frame):
        def __init__(self, parent=None, color='brown'):
            Frame.__init__(self, parent)
            self.pack(expand=YES, fill=BOTH)
            canv = Canvas(self, bg=color, relief=SUNKEN)
            canv.config(width=300, height=200)
            canv.config(scrollregion=(0, 0, 300, 1000))
            canv.config(highlightthickness=0)
    
            sbar = Scrollbar(self)
            sbar.config(command=canv.yview)
            canv.config(yscrollcommand=sbar.set)
            sbar.pack(side=RIGHT, fill=Y)
            canv.pack(side=LEFT, expand=YES, fill=BOTH)
    
            self.fillContent(canv)
            canv.bind('<Double-1>', self.onDoubleClick)
            self.canvas = canv
    
        def fillContent(self, canv):
            for i in range(10):
                canv.create_text(150, 50+(i*100), text='spam'+str(i), fill='beige')
    
        def onDoubleClick(self, event):
            print(event.x, event.y)
            print(self.canvas.canvasx(event.x), self.canvas.canvasy(event.y))
    
    if __name__ == '__main__':
        ScrolledCanvas().mainloop()
    

#### canvas绑定事件

    # gui28.py
    # -*- coding: UTF-8 -*-
    from Tkinter import *
    trace = False
    
    class CanvasEventDemo:
        def __init__(self, parent=None):
            canvas = Canvas(width=300, height=300, bg='beige')
            canvas.pack()
            canvas.bind('<ButtonPress-1>', self.on_start)
            canvas.bind('<B1-Motion>', self.on_grow)
            canvas.bind('<Double-1>', self.on_clear)
            canvas.bind('<ButtonPress-3>', self.on_move)
            self.canvas = canvas
            self.drawn = None
            self.kinds = [canvas.create_oval, canvas.create_rectangle]
    
        def on_start(self, event):
            self.shape =self.kinds[0]
            self.kinds = self.kinds[1:] + self.kinds[:1]
            self.start = event
            self.drawn = None
    
        def on_grow(self, event):
            canvas = event.widget
            if self.drawn:
                canvas.delete(self.drawn)
            object_id = self.shape(self.start.x, self.start.y, event.x, event.y)
            if trace:
                print(object_id)
            self.drawn = object_id
    
        def on_clear(self, event):
            event.widget.delete('all')
    
    
        def on_move(self, event):
            if self.drawn:
                if trace:
                    print(self.drawn)
                canvas = event.widget
                diffx, diffy = (event.x - self.start.x), (event.y - self.start.y)
                canvas.move(self.drawn, diffx, diffy)
                self.start = event
    
    if __name__ == '__main__':
        CanvasEventDemo()
        mainloop()
    

#### grid布局和pack布局实现自动扩展

    # gui29.py
    # -*- coding: UTF-8 -*-
    
    from Tkinter import *
    colors = ['red', 'green', 'orange', 'white', 'yellow', 'blue']
    
    def gridbox(root):
        Label(root, text='GRID').grid(columnspan=2)  # 跨两列
        row = 1
        for color in colors:
            lab = Label(root, text=color, relief=RIDGE, width=25)
            ent = Entry(root, bg=color, relief=SUNKEN, width=50)
            lab.grid(row=row, column=0, sticky=NSEW)  # 向4个方向扩展
            ent.grid(row=row, column=1, sticky=NSEW)
            root.rowconfigure(row, weight=1)  # 循环行自动扩展
            row += 1
        root.columnconfigure(0, weight=1)  # 第一列自动扩展
        root.columnconfigure(1, weight=1)  # 第二列自动扩展
    
    def packbox(root):
        Label(root, text='PACK').pack()
        for color in colors:
            row = Frame(root)
            lab = Label(row, text=color, relief=RIDGE, width=25)
            ent = Entry(row, bg=color, relief=SUNKEN, width=50)
            row.pack(side=TOP, expand=YES, fill=BOTH)
            lab.pack(side=LEFT, expand=YES, fill=BOTH)
            ent.pack(side=RIGHT, expand=YES, fill=BOTH)
    
    if __name__ == '__main__':
        root = Tk()
        gridbox(Toplevel(root))
        packbox(Toplevel(root))
        Button(root, text='Quit', command=root.quit).pack()
        mainloop()
    

### PIL：Python Imaging Library

#### 安装

    sudo apt-get install python-imaging
    或者
    sudo easy_install PIL
    

window系统选择

    pip install Pillow
    

图像操作

    import Image
    # 打开一个jpg图像文件，注意路径要改成你自己的:
    im = Image.open('/Users/michael/test.jpg')
    # 获得图像尺寸:
    w, h = im.size
    # 缩放到50%:
    im.thumbnail((w//2, h//2))
    # 把缩放后的图像用jpeg格式保存:
    im.save('/Users/michael/thumbnail.jpg', 'jpeg')
    

模糊效果

    import Image, ImageFilter
    
    im = Image.open('/Users/hanchang/test.jpg')
    im2 = im.filter(ImageFilter.BLUR)
    im2.save('/Users/hanchang/blur.jpg', 'jpeg')
    

自定义图像验证码

    import Image, ImageDraw, ImageFont, ImageFilter
    import random
    # 随机字母:
    def rndChar():
        return chr(random.randint(65, 90))
    
    # 随机颜色1:
    def rndColor():
        return (random.randint(64, 255), random.randint(64, 255), random.randint(64, 255))
    
    # 随机颜色2:
    def rndColor2():
        return (random.randint(32, 127), random.randint(32, 127), random.randint(32, 127))
    
    # 240 x 60:
    width = 60 * 4
    height = 60
    image = Image.new('RGB', (width, height), (255, 255, 255))
    # 创建Font对象:
    font = ImageFont.truetype('Arial.ttf', 36)
    # 创建Draw对象:
    draw = ImageDraw.Draw(image)
    # 填充每个像素:
    for x in range(width):
        for y in range(height):
            draw.point((x, y), fill=rndColor())
    # 输出文字:
    for t in range(4):
        draw.text((60 * t + 10, 10), rndChar(), font=font, fill=rndColor2())
    # 模糊:
    image = image.filter(ImageFilter.BLUR)
    image.save('code.jpg', 'jpeg');
    自定义字符串生成图片
    

``` from PIL import Image,ImageDraw,ImageFont,ImageOps <h1>生成图片</h1> def create\_image(str, line, name, dir): img\_size = (400, (line*20) + 10) img\_mode = 'RGB' img\_font = ImageFont.truetype('UbuntuMono-R.ttf', 14) img\_bg = '#FFF' img\_fg = '#000' img = Image.new(mode=img\_mode, size=img\_size, color=img\_bg) draw = ImageDraw.Draw(img) draw.text((10, 10), str, fill=img\_fg, font=img_font) img.save( dir + name + '.png', 'png') ```