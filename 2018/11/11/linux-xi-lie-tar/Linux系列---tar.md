---
title: Linux命令：tar
categories: 
  - Linux
tags:
  - 编程语言
  - Linux
date: 2018-11-11
---

```shell
tar -cvf log.tar log2018.log    #仅打包，不压缩！

tar -zcvf log.tar.gz log2018.log   #打包后，以 gzip 压缩
tar -jcvf log.tar.bz2 log2018.log  #打包后，以 bzip2 压缩

tar -zxvf /opt/soft/test/log.tar.gz # 解压gz
tar -jxvf /opt/soft/test/log.tar.bz2 # 解压bz2
```