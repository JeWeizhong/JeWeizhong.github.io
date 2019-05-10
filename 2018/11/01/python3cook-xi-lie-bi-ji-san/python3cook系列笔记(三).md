---
title: python3 cookbook笔记：第一章 数据结构和算法(三)---字典的用法(续)
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-01
mathjax: true

---


**写在开头：感谢译者们的对本书的翻译！非常棒的一本提升python技能的书，项目地址：https://github.com/yidao620c/python3-cookbook** 

1. `itertools.groupby()`可以根据指定字段来分组：
 
        ```python
        rows = [
        {'address': '5412 N CLARK', 'date': '07/01/2012'},
        {'address': '5148 N CLARK', 'date': '07/04/2012'},
        {'address': '5800 E 58TH', 'date': '07/02/2012'},
        {'address': '2122 N CLARK', 'date': '07/03/2012'},
        {'address': '5645 N RAVENSWOOD', 'date': '07/02/2012'},
        {'address': '1060 W ADDISON', 'date': '07/02/2012'},
        {'address': '4801 N BROADWAY', 'date': '07/01/2012'},
        {'address': '1039 W GRANVILLE', 'date': '07/04/2012'},
        ]
        ```
    我们按照date来分组：

        ```python
        from operator import itemgetter
        from itertools import groupby
        # Sort by the desired field first
        rows.sort(key=itemgetter('date'))
        # Iterate in groups
        for date, items in groupby(rows, key=itemgetter('date')):
        print(date)
        for i in items:
        print(' ', i)
        ```
    输出：

        ```python

        07/01/2012
                {'date': '07/01/2012', 'address': '5412 N CLARK'}
        {'date': '07/01/2012', 'address': '4801 N BROADWAY'}
        07/02/2012
                {'date': '07/02/2012', 'address': '5800 E 58TH'}
                {'date': '07/02/2012', 'address': '5645 N RAVENSWOOD'}
                {'date': '07/02/2012', 'address': '1060 W ADDISON'}
        07/03/2012
                {'date': '07/03/2012', 'address': '2122 N CLARK'}
        07/04/2012
                {'date': '07/04/2012', 'address': '5148 N CLARK'}
                {'date': '07/04/2012', 'address': '1039 W GRANVILLE'}
        ```

`groupby()` 函数扫描整个序列并且查找连续相同值（或者根据指定key 函数返回值相同）的元素序列。在每次迭代的时候，它会返回一个值和一个迭代器对象，这个迭代器对象可以生成元素值全部等于上面那个值的组中所有对象。

还有一个非常重要的准备步骤是要根据指定的字段将数据排序

1.  `itertools.compress()`,它以一个iterable对象和一个相对应的Boolean 选择器序列作为输入参数。然后输出iterable 对象中对
应选择器为True 的元素.

示例：

        ```python
        addresses = [
        '5412 N CLARK',
        '5148 N CLARK',
        '5800 E 58TH',
        '2122 N CLARK',
        '5645 N RAVENSWOOD',
        '1060 W ADDISON',
        '4801 N BROADWAY',
        '1039 W GRANVILLE',
        ]
        counts = [ 0, 3, 10, 4, 1, 7, 6, 1]
        # 
        >>> from itertools import compress
        >>> more5 = [n > 5 for n in counts]
        >>> more5
        [False, False, True, False, False, True, True, False]
        >>> list(compress(addresses, more5))
        ['5800 E 58TH', '1060 W ADDISON', '4801 N BROADWAY']
        >>>
        ```

3. `collections.namedtuple()` 函数对元组进行命名：

        ```python
        >>> from collections import namedtuple
        >>> Subscriber = namedtuple('Subscriber', ['addr', 'joined'])
        >>> sub = Subscriber('jonesy@example.com', '2012-10-19')
        >>> sub
        Subscriber(addr='jonesy@example.com', joined='2012-10-19')
        >>> sub.addr
        'jonesy@example.com'
        >>> sub.joined
        '2012-10-19'
        >>>
        ```
命名元组另一个用途就是作为字典的替代，因为字典存储需要更多的内存空间。如果你需要构建一个非常大的包含字典的数据结构，那么使用命名元组会更加高效。但是需要注意的是，不像字典那样，一个命名元组是不可更改的

4. `sum()` , `min()` , `max()`等函数可以接收迭代器作为参数：

        ```python 
        s = sum((x * x for x in nums)) # 显示的传递一个生成器表达式对象
        s = sum(x * x for x in nums) # 更加优雅的实现方式，省略了括号,而且省去了一个新建列表的命名空间
        ```

1. 合并多个字典或映射,先看示例：

        ```python
        a = {'x': 1, 'z': 3 }
        b = {'y': 2, 'z': 4 }
        ```

        ```python
        from collections import ChainMap
        c = ChainMap(a,b)
        print(c['x']) # Outputs 1 (from a)
        print(c['y']) # Outputs 2 (from b)
        print(c['z']) # Outputs 3 (from a)
        ```

一个`ChainMap` 接受多个字典并将它们在逻辑上变为一个字典。然后，这些字典并不是真的合并在一起了，`ChainMap` 类只是在内部创建了一个容纳这些字典的列表并重新定义了一些常见的字典操作来遍历这个列表.

如果出现重复键，那么第一次出现的映射值会被返回。因此，例子程序中的c['z'],总是会返回字典a 中对应的值，而不是b 中对应的值。

因此，对于字典的更新或删除操作总是影响的是列表中第一个字典。

与`update`不同，`ChainMap` 使用原来的字典，它自己不创建新的字典

**第一章完，下一章：字符串和文本**