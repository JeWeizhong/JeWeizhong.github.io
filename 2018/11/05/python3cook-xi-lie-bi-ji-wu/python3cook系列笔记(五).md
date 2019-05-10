---
title: python3 cookbook笔记：第四章 迭代器与生成器
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-05
mathjax: true

---


可迭代对象是一个比较新的词汇，而迭代是Python 最强大的功能之一。初看起来，你可能会简单的认为迭代只不过是处理序列中元素的一种方法。然而，绝非仅仅就是如此，还有很多你可能不知道的，比如创建你自己的迭代器对象，在itertools 模块中使用有用的迭代模式，构造生成器函数等等。

1. 使用`next()`可以实现手动手动迭代，其实任何迭代内部都是调用了`__next__()`方法迭代结束标识是在结尾处捕获了`StopIteration`

2. `yield` 语句可是创建一个可迭代对象

3. 函数`itertools.islice()`用于在迭代器和生成器上做切片操作:
   
    ```python
        >>> def count(n):
    ...         while True:
    ...             yield n
    ...             n += 1
    ...
    >>> c = count(0)
    >>> c[10:20]
    Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    TypeError: 'generator' object is not subscriptable
    >>> # Now using islice()
    >>> import itertools
    >>> for x in itertools.islice(c, 10, 20):
    ... print(x)
    ...
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    >>>
    ```
    迭代器和生成器不能使用标准的切片操作，因为它们的长度事先我们并不知道(并且也没有实现索引)。函数islice() 返回一个可以生成指定元素的迭代器，它通过遍
历并丢弃直到切片开始索引位置的所有元素。然后才开始一个个的返回元素，并直到切片结束索引位置。
    这里要着重强调的一点是islice() 会消耗掉传入的迭代器中的数据。必须考虑到迭代器是不可逆的这个事实。所以如果你需要之后再次访问这个迭代器的话，那你就得
先将它里面的数据放入一个列表中。

4. `itertools.dropwhile()` 函数,你给它传递一个函数对象和一个可迭代对象。它会返回一个迭代器对象，丢弃原有序列中直到函数返回Flase 之前的所有元素，然后返回后
面所有元素。

5. **排列组合的迭代：**迭代遍历一个集合中元素的所有可能的排列或组合。
   `itertools.permutations()`函数接受一个集合并产生一个元组序列，序列包括了集合中所有的排列组合(顺序不同也算是一种组合)：

    ```python
    >>> items = ['a', 'b', 'c']
    >>> from itertools import permutations
    >>> for p in permutations(items):
    ... print(p)
    ...
    ('a', 'b', 'c')
    ('a', 'c', 'b')
    ('b', 'a', 'c')
    ('b', 'c', 'a')
    ('c', 'a', 'b')
    ('c', 'b', 'a')
    >>>
    ```
    如果你想得到指定长度的所有排列，你可以传递一个可选的长度参数:

    ```python
    >>> for p in permutations(items, 2):
    ... print(p)
    ...
    ('a', 'b')
    ('a', 'c')
    ('b', 'a')
    ('b', 'c')
    ('c', 'a')
    ('c', 'b')
    >>>
    ```
    `itertools.combinations()`函数也是返回排列组合，但是忽略顺序，即，组合('a','b') 跟('b', 'a')

6. 内置的`enumerate() 函数`返回索引跟对应的元素
7. `zip()` 函数可以同时迭代多喝序列：

    ```python
    >>> xpts = [1, 5, 4, 2, 10, 7]
    >>> ypts = [101, 78, 37, 15, 62, 99]
    >>> for x, y in zip(xpts, ypts):
    ...     print(x,y)
    ...
    1 101
    5 78
    4 37
    2 15
    10 62
    7 99
    >>>

    ```

8. `isinstance(x, Iterable)` 检查某个元素是否是可迭代的