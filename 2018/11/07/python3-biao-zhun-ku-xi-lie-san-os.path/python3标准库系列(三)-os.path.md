---
title: python3 标准库系列(三)---os.path
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-07
mathjax: true

---

`os.path`模块比较简单，先记住下面这些常用的用就可以了：

```python
>>> import os
>>> path = '/Users/beazley/Data/data.csv'
>>> # 获取文件名
>>> os.path.basename(path)
'data.csv'
>>> # 获取文件所在目录名
>>> os.path.dirname(path)
'/Users/beazley/Data'
>>> # 多个元素拼接成一个链接
>>> os.path.join('tmp', 'data', os.path.basename(path))
'tmp/data/data.csv'
>>> # 将家目录符号~换成真实链接
>>> path = '~/Data/data.csv'
>>> os.path.expanduser(path)
'/Users/beazley/Data/data.csv'
>>> # 获取文件扩展名
>>> os.path.splitext(path)
('~/Data/data', '.csv')
>>># 检查文件或目录是否存在
>>> os.path.exists('/etc/passwd')
True
>>> # 检测是不是文件类型
>>> os.path.isfile('/etc/passwd')
True
>>> # 检测是不是目录类型
>>> os.path.isdir('/etc/passwd')
False
>>> # 检测是不是符号链接
>>> os.path.islink('/usr/local/bin/python3')
True
>>> # 返回一个绝对路径
>>> os.path.realpath('/usr/local/bin/python3')
'/usr/local/bin/python3.3'
>>># 获取文件大小
>>> os.path.getsize('/etc/passwd')
3669
>>> # 获取文件修改日期
>>> os.path.getmtime('/etc/passwd')
1272478234.0
>>> import time
>>> time.ctime(os.path.getmtime('/etc/passwd'))
'Wed Apr 28 13:10:34 2010'
>>> # 获取指定目录下的文件列表 
>>>os.listdir('somedir')
```