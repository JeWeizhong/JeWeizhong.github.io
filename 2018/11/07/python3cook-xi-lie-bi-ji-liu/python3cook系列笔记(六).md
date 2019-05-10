---
title: python3 cookbook笔记：第四章 文件与IO
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-07
mathjax: true

---

所有程序都要处理输入和输出。这一章将涵盖处理不同类型的文件，包括文本和二进制文件，文件编码和其他相关的内容。对文件名和目录的操作也会涉及到。

文本打开模式：

|模式|描述|
|:---:|:---|
|t|文本模式(默认)|
|r|以只读方式打开文件。这是默认模式。|
|rb|读取二进制文件|
|w|写入文件|
|wb|写入二进制到文件中|
|a|追加形式写入文件|
|w+|打开文件用于读写，文件指针在开始|
|a+|打开文件用于读写，追加模式|



- `open()` 函数默认是以”utf-8“编码打开文件，`encoding`参数可以指定编码：

```python
with open('somefile.txt', 'rt', encoding='latin-1') as f:
    ...
```
*ps:我们都指定写入文件是用的`f.write()`,但是`print()`函数也可以指定`file`参数来输出内容到文件中：*

```python
with open('d:/work/test.txt', 'wt') as f:
    print('Hello World!', file=f)
```

`newline`参数可以指定行分隔符。默认情况下，Python 会以统一模式处理换行符(，在Unix 和Windows 中是不一样的,分别是\n 和\r\n ):

```python
with open('somefile.txt', 'rt', newline='') as f:
    ...
```

- 读写一个gzip 或bz2 格式的压缩文件，跟普通读写差别不是很大，直接看示例就好。

读取压缩文件：

```python
# gzip compression
import gzip
with gzip.open('somefile.gz', 'rt') as f:
    text = f.read()
# bz2 compression
import bz2
with bz2.open('somefile.bz2', 'rt') as f:
    text = f.read()

```

写入压缩文件：

```python
with gzip.open('somefile.gz', 'wt', compresslevel=5) as f:
f.write(text)
```

`compresslevel`参数是指定压缩级别，默认的等级是9，也是最高的压缩等级。等级越低性能越好，但是数据压缩程度也越低。


- 获取固定长度，而不是一行一行迭代：

```python
from functools import partial
RECORD_SIZE = 32

with open('somefile.data', 'rb') as f:
    records = iter(partial(f.read, RECORD_SIZE), b'')
    for r in records:
        ...
```

- 本章也介绍了`os.path`模块的使用，请参见**python3标准库系列笔记**
- 创建临时文件和文件夹，使用完以后会自动销毁：

```python
from tempfile import TemporaryFile
# 创建临时文件
with TemporaryFile('w+t') as f:
    # Read/write to the file
    f.write('Hello World\n')
    f.write('Testing\n')

    # 回到文件开始，读取文件
    f.seek(0)
    data = f.read()
```
或者：

```python
f = TemporaryFile('w+t')
# Use the temporary file
...
f.close()
# File is destroyed
```

在大多数Unix 系统上，通过`TemporaryFile()` 创建的文件都是匿名的，甚至连目录都没有。如果你想打破这个限制，可以使用`NamedTemporaryFile()` 来代替。比如：

```python
from tempfile import NamedTemporaryFile
with NamedTemporaryFile('w+t') as f:
    print('filename is:', f.name)
    ...
# File automatically destroyed
```

这里，被打开文件的`f.name` 属性包含了该临时文件的文件名。当你需要将文件
名传递给其他代码来打开这个文件的时候，这个就很有用了。和`TemporaryFile()` 一样，结果**文件关闭**时会被自动删除掉。如果你不想这么做，可以传递一个关键字参数`delete=False` 即可。比如：

```
with NamedTemporaryFile('w+t', delete=False) as f:
    print('filename is:', f.name)
    ...
```

为了创建一个临时目录，可以使用`tempfile.TemporaryDirectory()` 。比如：

```python
from tempfile import TemporaryDirectory

with TemporaryDirectory() as dirname:
    print('dirname is:', dirname)
    # Use the directory
    ...
# Directory and all contents destroyed
```

- 序列化Python 对象

以下内容来源于廖老师的[**python教程**](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000),非常感谢廖老师出了如此优秀的入门教程

在程序运行的过程中，所有的变量都是在内存中，比如，定义一个`dict`：

```python
d = dict(name='Bob', age=20, score=88)
```

可以随时修改变量，比如把`name`改成`'Bill'`，但是一旦程序结束，变量所占用的内存就被操作系统全部回收。如果没有把修改后的`'Bill'`存储到磁盘上，下次重新运行程序，变量又被初始化为`'Bob'`。

我们把变量从内存中变成可存储或传输的过程称之为序列化，在Python中叫`pickling`，在其他语言中也被称之为`serialization`，`marshalling`，`flattening`等等，都是一个意思。

序列化之后，就可以把序列化后的内容写入磁盘，或者通过网络传输到别的机器上。

反过来，把变量内容从序列化的对象重新读到内存里称之为反序列化，即`unpickling`。

Python提供两个模块来实现序列化：`cPickle`和`pickle`。这两个模块功能是一样的，区别在于`cPickle`是C语言写的，速度快，`pickle`是纯Python写的，速度慢，跟`cStringIO`和`StringIO`一个道理。用的时候，先尝试导入`cPickle`，如果失败，再导入`pickle`：

```python
try:
    import cPickle as pickle
except ImportError:
    import pickle
```

首先，我们尝试把一个对象序列化并写入文件：

```python
>>> d = dict(name='Bob', age=20, score=88)
>>> pickle.dumps(d)
"(dp0\nS'age'\np1\nI20\nsS'score'\np2\nI88\nsS'name'\np3\nS'Bob'\np4\ns."
```

`pickle.dumps()`方法把任意对象序列化成一个`str`，然后，就可以把这个`str`写入文件。或者用另一个方法`pickle.dump()`直接把对象序列化后写入一个`file-like Object`：

```python
>>> f = open('dump.txt', 'wb')
>>> pickle.dump(d, f)
>>> f.close()
```

看看写入的`dump.txt`文件，一堆乱七八糟的内容，这些都是Python保存的对象内部信息。

当我们要把对象从磁盘读到内存时，可以先把内容读到一个`str`，然后用`pickle.loads()`方法反序列化出对象，也可以直接用`pickle.load()`方法从一个`file-like Object`中直接反序列化出对象。我们打开另一个Python命令行来反序列化刚才保存的对象：

```python
>>> f = open('dump.txt', 'rb')
>>> d = pickle.load(f)
>>> f.close()
>>> d
{'age': 20, 'score': 88, 'name': 'Bob'}
```

变量的内容又回来了！

当然，这个变量和原来的变量是完全不相干的对象，它们只是内容相同而已。

*Pickle的问题和所有其他编程语言特有的序列化问题一样，就是它只能用于Python，并且可能不同版本的Python彼此都不兼容，因此，只能用Pickle保存那些不重要的数据，不能成功地反序列化也没关系。*



为了从字节流中恢复一个对象，使用`picle.load()` 或`pickle.loads()` 函数。比如：

```python
# Restore from a file
f = open('somefile', 'rb')
data = pickle.load(f)
# Restore from a string
data = pickle.loads(s)
```


