---
title: python3 cookbook笔记：第一章 数据结构和算法(二)---字典的用法
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-10-31
mathjax: true

---


**写在开头：感谢译者们的对本书的翻译！非常棒的一本提升python技能的书，项目地址：https://github.com/yidao620c/python3-cookbook** 

1. `collections` 模块中的`defaultdict`函数能够构造一个值为列表的字典：

    ```python
    from collections import defaultdict
    d = defaultdict(list)
    d['a'].append(1)
    d['a'].append(2)
    d['b'].append(4)
    d = defaultdict(set)
    d['a'].add(1)
    d['a'].add(2)
    d['b'].add(4)
    ```
    简化版：

    ```python
    d = defaultdict(list)
    for key, value in pairs:
    d[key].append(value)
    ```
2. 字段的键存储是无序的，如果想要一个键顺序是一定的字典，可以这样做：
    ```
    from collections import OrderedDict
    d = OrderedDict()
    ```
    但这样做内存占用比较大

3. 如果你在一个字典上执行普通的数学运算，你会发现它们仅仅作用于键，而不是值：

    ```python
    prices = {
    'ACME': 45.23,
    'AAPL': 612.78,
    'IBM': 205.55,
    'HPQ': 37.20,
    'FB': 10.75
    }
    min(prices) # Returns 'AAPL'
    max(prices) # Returns 'IBM'
    ```
    想要对值进行操作，可以这样：
    ```
    min(prices, key=lambda k: prices[k]) # Returns 'FB'
    max(prices, key=lambda k: prices[k]) # Returns 'AAPL'
    ```
4. 对两个字典进行运算：
   
    ```python
    # Find keys in common
    a.keys() & b.keys() # { 'x', 'y' }
    # Find keys in a that are not in b
    a.keys() - b.keys() # { 'z' }
    # Find (key,value) pairs in common
    a.items() & b.items() # { ('y', 2) }
    ```
    ~~ps：如果想要对列表消除重复元素可以转换位集合：~~

    ```python
    >>> a
    [1, 5, 2, 1, 9, 1, 5, 10]
    >>> set(a)
    {1, 2, 10, 5, 9}
    >>>
    ```
    如果想要对一个复杂类型的列表去重复，就要用到一个稍微复杂一点的函数：

    ```python
    def dedupe(items, key=None):
        seen = set()
        for item in items:
            val = item if key is None else key(item)
            if val not in seen:
                yield item
                seen.add(val)

    >>> a = [ {'x':1, 'y':2}, {'x':1, 'y':3}, {'x':1, 'y':2}, {'x':2, 'y':4}]
    >>> list(dedupe(a, key=lambda d: (d['x'],d['y'])))
    [{'x': 1, 'y': 2}, {'x': 1, 'y': 3}, {'x': 2, 'y': 4}]
    >>> list(dedupe(a, key=lambda d: d['x']))
    [{'x': 1, 'y': 2}, {'x': 2, 'y': 4}]
    >>>
    ```

5. 按照关键字对字典进行排序：
   
    ```python
    rows = [
    {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003},
    {'fname': 'David', 'lname': 'Beazley', 'uid': 1002},
    {'fname': 'John', 'lname': 'Cleese', 'uid': 1001},
    {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
    ]

    from operator import itemgetter
    rows_by_fname = sorted(rows, key=itemgetter('fname'))
    rows_by_uid = sorted(rows, key=itemgetter('uid'))
    print(rows_by_fname)
    print(rows_by_uid)
    ```

`itemgetter()` 有时候也可以用`lambda` 表达式代替，比如：

    ```python

    rows_by_fname = sorted(rows, key=lambda r: r['fname'])
    rows_by_lfname = sorted(rows, key=lambda r: (r['lname'],r['fname']))
    ```
理论上讲，`operator.attrgetter()`与 `itemgetter()` 函数的作用是一样，书中说**排序不支持原生比较的对象**要用`attrgetter()`函数，反正我也不知道什么是**不支持原生比较的对象**

**下一节：字典的用法(续)**