---
title: perl 单行命令
categories: 
  - perl
tags:
  - 编程语言
  - perl
date: 2018-02-13
---
**与One-Liner相关的perl参数**
- -a 自动分隔模式，用空格分隔$并保存在@F中，也就是@F=split //, $
- -F 指定-a的分隔符
- -l 对输入的内容进行自动chomp，对输出的内容自动加换行符
- -n 相当于while(<>)
- -e 执行命令，也就是脚本
- -p 自动循环+输出，也就是while(<>){命令（脚本）; print;}

![完整版本](http://files.jb51.net/file_images/article/201709/2017910200851051.png?201781020923)

1. 基本格式：perl -参数 '命令' 输入文件

2. `perl -e`为必须要写的参数

3. `perl -pe` 可用于读取文件每行，并按照给定的命令进行处理，最后输出；如将文件1.txt中的aaa替换为AAA
```
    perl -pe 's/aaa/AAA/g' 1.txt
```
4. `perl -l`参数几乎可以跟n搭配代替perl经常用的`while(<>){chomp;}`语法

5. 如果需要处理tab分割的文件的每一行内容，那么`perl -alne`参数几乎可以说是必备的，例如:`while(<>){chomp;@F=split /\s+/,$_;print "$F[0]\n"}`相当于`perl -alne 'print $F[0]'`
6. perl单行命令脚本里的变量都不需要预先声明，如想打印出每空行，并且每行以行数开头
```
    perl -ne 'print ++$a." $_" if /./'
```
7. perl单行命令有时优于sed/grep等shell命令是由于其优秀的正则匹配，通常简单的匹配可以如：匹配上的行号，模仿grep -c的功能：
```
    perl -lne '$a++ if /regex/; END {print $a+0}'
```
8. perl单行命令可以使用perl的模块，如使用sum函数的模块：
```
    perl -MList::Util=sum -alne 'print sum @F'
```
9. perl也可以像awk一样使用END命令，如打印出文件中总单词个数
```
    perl -alne '$t += @F; END { print $t}'
```
10. perl也可以使用`map{}`等函数，如打印出匹配上的单词的总个数
```
perl -alne 'map { /regex/ && $t++ } @F; END { print $t }'
```
11. perl单行命令可以说是将perl的简洁用到了极致，如打印出匹配上的行：
```
perl -ne '/regex/ && print'
```
12. perl单行命令能像perl一样灵活的使用则正表达式
```
perl -ne 'print if /^\d+$/'
```
以上例子均出自于 http://www.catonmat.net/blog/perl-one-liners-explained-part-one/

使用perl来处理数据的我们，会一点Perl one line可以有效的减少编写重复命令的时间，尤其是那些就用1-2次就不会用的脚本，尤其在window系统下不方便使用shell命令的时候。

PS.当然在windows系统下也可以借用git模拟Unix命令环境