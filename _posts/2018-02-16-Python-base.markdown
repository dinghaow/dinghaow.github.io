---
layout:     post
title:      "Python base"
subtitle:   " \"Python基础语法\""
date:       2018-02-16 12:00:00
author:     "WangDinghao"
header-img: "img/post-bg-e2e-ux.jpg"
tags:
    - Python
---


# 语法基础

![python base](img/in-post/post-python-base/Python_1.png)

# 常用函数解析

```python
# -*- coding: utf-8 -*-

import os

"""
os.walk()  函数解析
    os.walk(path) 返回的是一个由3个元祖组成的列表
    [(当前目录列表),(子目录列表),(文件列表)]
"""

filePath = '/Users/wangdinghao/Documents/file'
svn_tree = os.walk(filePath)

for dirName,subDir,fileList in svn_tree:
    print(dirName)
    print(subDir)
    print(fileList)
    print("--------分隔线-----------")

```

# 案例
## 批量获取姓名和考号
```python
# -*- coding: utf-8 -*-

import os

"""
批量获取姓名和考号
"""

filenames = []
for a,b,files in os.walk('test'):          #获取test目录下的所有文件
    if files:
        filenames.append([file[:-4] for file in files])       #文件扩展名为三个字母

fname = 'testexam'                         #指定生成电子表格的文件名
i = 0
for files in filenames:
    f = open(fname + str(i)+'\t'+name[:-2]+'\n')              #打开指定的文件
    for name in files:
        f.write(name[-2:]+'\t'+name[:-2]+'\n')                #写入姓名和考号
    f.close()
    i += 1
print("成功生成")


```

## 批量文件重命名

```python
# -*- coding: utf-8 -*-

import  os

"""
批量文件重命名
"""

perfix = 'Python'     #perfix为重命名后的文件起始字符
length = 2            #length为除去perfix后，文件名要达到的长度
base = 1              #文件名的起始数
format = 'mdb'        #文件的后缀

#函数PadLeft将文件名补全到指定长度
# str为要补全的字符
# num为要达到的长度
#padstr 未达到的长度所添加的字符
def PadLeft(str,num,padstr):
    stringlength = len(str)
    n = num - stringlength
    if n >=0:
        str=padstr*n + str
        return str


#为了避免操作错误，提示用户
print('the files in %s will be renamed' % os.getcwd())
all_files = os.listdir()
print([f for f in all_files if os.path.isfile(f)])  #输出当前目录下的所有文件名
input = input('press y to continue\n')  #获取用户输入
if input.lower() != 'y'    #判断用户输入是否是y
    exit()
filenames = os.listdir(os.curdir)
#基数减一，为了下面i = i+1 在第一次执行时等于基数
i = base - 1
for filename in filenames:
    i = i+1
    # 判断当前路径是否为文件,并且不是rename.py
    if filename != 'rename.py' and os.path.isfile(filename):
        name = str(i)
        name = PadLeft(name,length,'0')
        t = filename.split('.')
        m = len(t)
        if format == ''
            os.rename(filename,perfix+name+'.'+t[m-1])
        else:
            if t[m-1] == format:
                os.rename(filename,perfix+name+'.'+t[m-1])
            else:
                i=i-1
        all_files = os.listdir(os.getcwd())
        print([f for f in all_files if os.path.isfile(f)])  输出当前目录下的所有文件名

```

## "360"缩率版

```python
# -*- coding: utf-8 -*-

import tkinter
import tkinter.messagebox,tkinter.simpledialog
import os,os.path
import threading


rubbishExt = ['.tmp','.bak','.old','.wbk','.xlk','._mp','.log','.gid','.chk','.syd','.$$$','.@@@','.@@@','.~*']

class Window:
    def __init__(self):
        self.root = tkinter.Tk()

        #创建菜单
        menu = tkinter.Menu(self.root)

        #创建"系统"子菜单
        submenu = tkinter.Menu(menu,tearoff=0)
        submenu.add_command(label='关于...',command = self.MenuAbout)
        submenu.add_separator()
        submenu.add_command(label="退出",command = self.MenuExit)
        menu.add_cascade(label="系统",menu=submenu)

        #创建"清理"子菜单
        submenu = tkinter.Menu(menu,tearoff=0)
        submenu.add_command(label="扫描垃圾文件",command = self.MenuScanRubbish)
        submenu.add_command(label="删除垃圾文件",command = self.MenuDelRubbish)
        menu.add_cascade(label="清理",menu=submenu)

        #创建"查找"子菜单
        submenu = tkinter.Menu(menu,tearoff=0)
        submenu.add_command(label="搜索大文件",command = self.MenuScanBigFile)
        submenu.add_separator()
        submenu.add_command(label=" 按名称搜索文件",command = self.MenuSearchFile)
        menu.add_cascade(label="搜索",menu=submenu)

        self.root.config(menu=menu)  

        #创建标签，用于显示状态信息
        self.progress = tkinter.Label(self.root,anchor = tkinter.W,text='状态',bitmap='hourglass',compound='left')
        self.progress.place(x=10,y=370,width=480,height=350)

        #创建文本框，显示文件列表
        self.flist = tkinter.Text(self.root)
        self.flist.place(x=10,y=10,width=480,height=350)

        #为文本框添加垂直滚动条
        self.vscroll = tkinter.Scrollbar(self.flist)
        self.vscroll.pack(side = 'right',fill = 'y')
        self.flist['yscrollcommand'] = self.vscroll.set
        self.vscroll['command'] = self.flist.yview

    # 扫描垃圾文件
    def ScanRubbish(self,scanpath):
        global rubbishExt
        total = 0
        filesize = 0
        for drive in scanpath:
            for root, dirs, fils in os.walk(drive):
                try:
                    for fil in files:
                        filesplit = os.path.splitext(fil)
                        if filesplit[1] == '':  # 若文件无扩展名
                            continue
                        try:
                            if rubbishExt.index(filesplit[1]) >= 0:  # 扩展名在垃圾文件列表中
                                fname = os.path.join(os.path.abspath(root), fil)
                                filesize += os.path.getsize(fname)
                                if total % 20 == 0:
                                    self.flist.delete(0.0, tkinter.END)
                                self.flist.insert(tkinter.END, fname + '\n')
                                l = len(fname)
                                if l > 60:
                                    self.progress['text'] = fname[:30] + '...' + fname[l - 30:l]
                                else:
                                    self.progress['text'] = fname
                                total += 1  # 计数
                        except ValueError:
                            pass
                except Exception as e:
                    print(e)
                    pass
            self.progress['text'] = "找到 %s 个垃圾文件，共占用 %.2f M磁盘空间" % (total, filesize / 1024 / 1024)

    #删除垃圾文件
    def DeleteRubbish(self,scanpath):
        global  rubbishExt
        total = 0
        filesize = 0
        for drive in scanpath:
            for root,dirs,files in os.walk(drive):
                try:
                    for fil in files:
                        filesplit = os.path.splitext(fil)
                        if filesplit[1]  == '':    #若文件无扩展名
                            continue
                        try:
                            if rubbishExt.index(filesplit[1]) >=0 :
                                fname = os.path.join(os.path.abspath(root),fil)
                                filesize += os.path.getsize(fname)
                                try:
                                    os.remove(fname)
                                    l = len(fname)
                                    if l > 50:
                                        fname = fname[:25] + '...' +fname[l-25:l]
                                        if total % 15 == 0 :
                                            self.flist.delete(0.0,tkinter.END)
                                            self.flist.insert(tkinter.END,'Deleted'+fname+ '\n')
                                            self.progress['text'] = fname
                                            total += 1     #计数
                                except:          #不能删除，则跳过
                                    pass
                        except ValueError:
                            pass
                except Exception as e:
                    print(e)
                    pass

    def SearchFile(self,fname):
        total = 0
        fname = fname.upper()
        for drive in GetDrives():
            for root,dirs,files in os.walk(drive):
                for fil in fils:
                    try:
                        fn = os.path.abspath(os.path.join(root,fil))
                        l = len(fn)
                        if l > 50:
                            self.progress['text'] = fn[:25] + '...' + fn[l-25:l]
                        else:
                            self.progress['text'] = fn

                        if fil.upper().find(fname) >= 0:
                            total +=1
                            self.flist.insert(tkinter.END,fn +'\n')
                    except:
                        pass



    def ScanBigFile(self,filesize):
        total = 0
        filesize = filesize * 1024 * 1024
        for drive in GetDrives():
            for root,dirs,files in os.walk(drive):
                for fil in files:
                    try:
                        fname =  os.path.abspath(os.path.join(root,fil))
                        fsize = os.path.getsize(fname)
                        self.progress['text'] = fname     #在状态标签中显示每一个遍历的文件
                        if fsize >= filesize:
                            total += 1
                            self.flist.insert(tkinter.END,'%s,[%.2f M]\n' % (fname,fsize/1024/1024))
                    except:
                        pass

    def MainLoop(self):
        self.root.title("FindFat")
        self.root.minsize(500,400)
        self.root.maxsize(500,400)
        self.root.mainloop()

    def MenuAbout(self):
        tkinter.messagebox.showinfo("Findfat","这是使用Python编写的windows优化程序。\n 欢迎使用并提出宝贵意见！")

    def MenuExit(self):
        self.root.quit();

    def MenuScanRubbish(self):
        result = tkinter.messagebox.askquestion("Findfat","扫描垃圾文件将需要较长时间，是否继续？")
        if result == 'no':
            return
        tkinter.messagebox.showinfo("Findfat","马上开始扫描垃圾文件！")
        #self.ScanRubbish()
        self.drives = GetDrives()
        t = threading.Thread(target = self.ScanRubbish,args=(self.drives,))   # 创建线程
        t.start()                                                             # 开始线程

    def MenuDelRubbish(self):
        result = tkinter.messagebox.askquestion("Findfat","删除垃圾文件将需要较长时间，是否继续？")
        if result == 'no':
            return
        tkinter.messagebox.showinfo("Findfat","马上开始删除垃圾文件！")
        self.drives = GetDrives()
        t = threading.Thread(target=self.DeleteRubbish,args=(self.drives,))
        t.start()

    def MenuScanBigFile(self):
        s = tkinter.simpledialog.askinteger('Findfat','请设置大文件的大小(M)')
        t = threading.Thread(target=self.ScanBigFile,args=(s,))   #将用户输入的文件小大s作为元给传入
        t.start()

    def MenuSearchFile(self):
        s = tkinter.simpledialog.askstring('Findfat','请输入文件名的部分字符')
        t = threading.Thread(target=self.SearchFile,args=(s,))
        t.start()

def GetDrives():
    drives= []
    for i in range(65,91):
        vol = chr(i) + ':/'
        if os.path.isdir(vol):
            drives.append(vol)
    return  tuple(drives)

if __name__ == "__main__":
    window = Window()
    window.MainLoop()


```


