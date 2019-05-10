
---
title: AWK简明教程
categories: 
  - Linux
tags:
  - 编程语言
  - Linux
  - AWK
date: 2018-02-16
---

##### awk命令格式和选项
语法形式
```
awk [options] 'script' var=value file(s)
awk [options] -f scriptfile var=value file(s)
```
常用命令选项

- -F fs   fs指定输入分隔符，fs可以是字符串或正则表达式，如-F:
- -v var=value   赋值一个用户定义变量，将外部变量传递给awk
- -f scripfile  从脚本文件中读取awk命令
- -m[fr] val   对val值设置内在限制，-mf选项限制分配给val的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用。

**工作原理**
```
hawk 'BEGIN{ commands } pattern{ commands } END{ commands }'
```
##### 内建变量

 变量| 详细说明
---|---
$0	|当前记录（这个变量中存放着整个行的内容）
$1~$n	|当前记录的第n个字段，字段间由FS分隔
FS	|输入字段分隔符 默认是空格或Tab (**也可以是-F**)
NF	|当前记录中的字段个数，就是有多少列
NR	|已经读出的记录数，就是行号，从1开始，如果有多个文件话，这个值也是不断累加中。
FNR	|当前记录数，与NR不同的是，这个值会是各个文件自己的行号
RS  |输入的记录分隔符， 默认为换行符
OFS	|输出字段分隔符， 默认也是空格
ORS	|输出的记录分隔符，默认为换行符
FILENAME	|当前输入文件的名字
##### 模式匹配
```
$ awk '$6 ~ /FIN|TIME/ || NR==1 {print NR,$4,$5,$6}' OFS="\t" netstat.txt
```

##### 统计
下面的命令计算所有的C文件，CPP文件和H文件的文件大小总和。
```
$ ls -l  *.cpp *.c *.h | awk '{sum+=$5} END {print sum}'
2511401
```
统计每个用户的进程的占了多少内存
```
$ ps aux | awk 'NR!=1{a[$1]+=$6;} END { for(i in a) print i ", " a[i]"KB";}'
dbus, 540KB
mysql, 99928KB
www, 3264924KB
root, 63644KB
hchen, 6020KB
```
**++控制结构和脚本语言待补充==++**

**参考链接：**

http://man.linuxde.net/awk
https://coolshell.cn/articles/9070.html