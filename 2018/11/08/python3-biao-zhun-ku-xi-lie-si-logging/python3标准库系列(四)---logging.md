---
title: python3 标准库系列(四)---logging
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-08
mathjax: true

---

`logging`模块可以给脚本添加日志功能。

下面是一个输出日志的简单版：

```python
import logging

# levelname 输出等级
# asctime 时间
# lineno 所在行
# module 所在模块名
# filename 日志输出函数的模块的文件名，注意不是参数 filename
# message 输出的内容
logging.basicConfig(format='%(levelname)s %(asctime)s,line %(lineno)s:\t%(message)s',
                    level=logging.INFO,
                    datefmt='%Y-%m-%d %H:%M:%S') # 可以加filename=logfile参数将日志输出到文件中

logging.critical('当发生严重错误，导致应用程序不能继续运行时记录的信息')
logging.error('由于一个更严重的问题导致某些功能不能正常运行时记录的信息')
logging.warning('当某些不期望的事情发生时记录的信息（如，磁盘可用空间较低），但是此时应用程序还是正常运行的')
logging.info('信息详细程度仅次于DEBUG，通常只记录关键节点信息，用于确认一切都是按照我们预期的那样进行工作')
logging.debug('最详细的日志信息，典型应用场景是 问题诊断')

```
日志调用（`critical()`, `error()`, `warning()`, `info()`, `debug()`）以降序方式表示不同的严重级别,默认是`warning`。

下面的是一个自己用的以天为单位的输出日志的函数：

```python
import re
import logging

from logging.handlers import TimedRotatingFileHandler
from logging.handlers import RotatingFileHandler

def log_init(logfile):
    log_fmt = '%(asctime)s\tFile \"%(filename)s\",line %(lineno)s\t%(levelname)s: %(message)s'
    formatter = logging.Formatter(log_fmt)
    # 可以设置间隔时间，这里是一天生成一个文件
    log_file_handler = TimedRotatingFileHandler(filename=logfile, when="D", interval=1, backupCount=7)
    log_file_handler.suffix = "%Y-%m-%d.log"
    # filehanlder.suffix的格式必须这么写，才能自动删除旧文件
    # 如果设定是天，就必须写成“%Y-%m-%d.log”，写成其他格式会导致删除旧文件不生效

    log_file_handler.setFormatter(formatter)
    # log_file_handler.setLevel(logging.DEBUG)
    logging.getLogger().setLevel(logging.DEBUG)
    logging.getLogger().addHandler(log_file_handler)
    '''
    这里是需要运行的程序，需要按照文件格式删除log文件，需要启用 extMatch、removeHandle函数
    '''
    # logging.info('this is a loggging info message')
    # logging.debug('this is a loggging debug message')
    # logging.warning('this is loggging a warning message')
    # logging.error('this is an loggging error message')
    # logging.critical('this is a loggging critical message')
```

修改所调用的模块中的日志设置：

```python 
# somelib.py
import logging

log = logging.getLogger(__name__) # 默认就是： __name__ 只的当前的模块
log.addHandler(logging.NullHandler())
# Example function (for testing)
def func():
    log.critical('A Critical Error!')
    log.debug('A debug message')
```

下面我们调用这个模块：

```python
>>> import logging
# 这是当前脚本的日志设置
>>> logging.basicConfig(level=logging.ERROR)
>>> import somelib
>>> somelib.func()
CRITICAL:somelib:A Critical Error!
>>> # 指定模块名，改变该模块的输出级别
>>> logging.getLogger('somelib').level=logging.DEBUG
>>> somelib.func()
CRITICAL:somelib:A Critical Error!
DEBUG:somelib:A debug message
>>>
```

在这里，根日志被配置成仅仅输出`ERROR` 或更高级别的消息。不过，`somelib` 的日志级别被单独配置成可以输出debug 级别的消息，它的优先级比全局配置高。像这
样更改单独模块的日志配置对于调试来讲是很方便的，因为你无需去更改任何的全局日志配置——只需要修改你想要更多输出的模块的日志等级。