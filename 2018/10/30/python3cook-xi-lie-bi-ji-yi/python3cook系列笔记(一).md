---
title: python3 cookbook笔记：第一章 数据结构和算法(一)---解压列表
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-10-30
mathjax: true

---


**写在开头：感谢译者们的对本书的翻译！非常棒的一本提升python技能的书，项目地址：https://github.com/yidao620c/python3-cookbook** 

在python中任何可迭代的对象(**列表，元组，字符串，文件，迭代器，生成器等**)都可以直接进行解压并赋值，**只要保证**前后的元素是一样的，比如：
```
>>> p=(4,5)
>>> x,y=p
>>> x
4
>>> y
5
# 嵌套序列也一样
>>> data = [ 'ACME', 50, 91.1, (2012, 12, 21) ]
>>> name, shares, price, date = data
>>> name
'ACME'
>>> date
(2012, 12, 21)
>>> name, shares, price, (year, mon, day) = data
>>> name
'ACME'
>>> year
2012
>>> mon
12
>>> day
21
>>>
```

如果只想要部分，可以中`_`占位一个变量，`*_`占位多个变量(返回一个列表)，这些变量最后会被丢弃：
```
>>> record = ('ACME', 50, 123.45, (12, 18, 2012))
>>> name, *_, (*_, year) = record
>>> name
'ACME'
>>> year
2012
>>>
```
当然`*_`这个模式也可以不用下划线占位，而是给定一个变量名字，来获取剩余变量的列表：
```
>>> items = [1, 10, 7, 4, 5, 9]
>>> head, *tail = items
>>> head
1
>>> tail
[10, 7, 4, 5, 9]
>>>
```

*拓展阅读：*

实现一个优先级队列:
```
import heapq

class PriorityQueue:
    def __init__(self):
        self._queue = []
        self._index = 0
    def push(self, item, priority):
        heapq.heappush(self._queue, (-priority, self._index, item))
        self._index += 1
    def pop(self):
        return heapq.heappop(self._queue)[-1]
```
使用方式：
*小声bb：这本书喜欢用类，自己要抓紧补一下类的知识了*
```
>>> class Item:
...     def __init__(self, name):
...         self.name = name
...     def __repr__(self):
...         return 'Item({!r})'.format(self.name)
...
>>> q = PriorityQueue()
# 传入：元素，优先级
>>> q.push(Item('foo'), 1)
>>> q.push(Item('bar'), 5)
>>> q.push(Item('spam'), 4)
>>> q.push(Item('grok'), 1)
>>> q.pop()
Item('bar')
>>> q.pop()
Item('spam')
>>> q.pop()
Item('foo')
>>> q.pop()
Item('grok')

```

**下一节：字典的用法**