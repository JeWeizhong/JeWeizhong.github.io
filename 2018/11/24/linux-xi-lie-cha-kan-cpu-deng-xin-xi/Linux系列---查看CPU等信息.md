---
title: (转)linux下查看CPU、内存、磁盘信息
categories: 
  - Linux
tags:
  - 编程语言
  - Linux
date: 2018-11-24
---

文章内容来源：https://www.cnblogs.com/xubiao/p/6497533.html

**查看CPU信息**


总核数 = 物理CPU个数 X 每颗物理CPU的核数

总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数


**查看物理CPU个数**
```sh
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
```

**查看每个物理CPU中core的个数(即核数)**
```sh
cat /proc/cpuinfo| grep "cpu cores"| uniq
```

**查看逻辑CPU的个数**
```sh
cat /proc/cpuinfo| grep "processor"| wc -l
```

**查看CPU信息（型号）**
```sh
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
```
 
**查看CPU的负载**


**平均负载**是指上一分钟同时处于就绪状态的平均进程数。在CPU中可以理解为CPU可以并行处理的任务数量，就是CPU个数X核数。

如果CPU Load等于CPU个数乘以核数，那么就说CPU正好满负载，再多一点，可能就要出问题了，有些任务不能被及时分配处理器，那要保证性能的话，最好要小于CPU个数X核数X0.7。

Load Average是指CPU的Load。它所包含的信息是在一段时间内CPU正在处理及等待CPU处理的进程数之和的统计信息，也就是CPU使用队列的长度的统计信息。

Load Average的值应该小于CPU个数X核数X0.7，Load Average会有3个状态平均值，分别是1分钟、5分钟和15分钟平均Load。

如果1分钟平均出现大于CPU个数X核数的情况，还不需要担心；如果5分钟的平均也是这样，那就要警惕了；15分钟的平均也是这样，就要分析哪里出现问题，防范未然。

  
**查看内存信息**

```sh
$ cat /proc/meminfo
$ free 
``` 

**查看磁盘信息**
```sh
$ fdisk -l
$ iostat -x 10    #查看磁盘IO的性能
```