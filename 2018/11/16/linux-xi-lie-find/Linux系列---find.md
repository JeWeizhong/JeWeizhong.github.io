---
title: Linux命令：find
categories: 
  - Linux
tags:
  - 编程语言
  - Linux
date: 2018-11-16
---

语法：
```
find *[directory-list]* *[option]* *[expression]*
```
常用
```
find dir -name file1
```
**选项：**

- `-i` 忽略大小写

- `-depth`：从指定目录下最深层的子目录开始查找； 

- `-expty`：寻找文件大小为0 Byte的文件，或目录下没有任何子目录或文件的空目录

- `-name`：指定字符串作为寻找文件或目录的范本样式；

- `-anewer`：查找其存取时间较指定文件或目录的存取时间 更接近现在的文件或目录；

- `-newer`：查找其更改时间较指定文件或目录的更改时间更接近现在的文件或目录；

- `-maxdepth`：设置最大目录层级； 

- `-mindepth`：设置最小目录层级；

- `-regex`：正则表达式

- `-type`：只寻找符合指定的文件类型的文件:
    
    - `b` 特殊的块文件
    - `c` 特殊的字符文件
    - `d` 目录文件
    - `f` 普通文件
    - `I` 符号链接
    - `p` FIFO(命名管道)
    - `s` 套接字