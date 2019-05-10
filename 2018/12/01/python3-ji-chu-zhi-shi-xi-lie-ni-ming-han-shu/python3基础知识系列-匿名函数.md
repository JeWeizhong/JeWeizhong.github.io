---
title: python3基础知识系列--匿名函数
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-12-01
mathjax: true

---

匿名函数其实就是lambda函数，不需要显示定义函数：

```python
f = lambda x: x*x
f(6)
# 36
```

这个等价于：

```python
def f(x):
    return x*x
```

但是匿名函数有一个限制就是只能写一个表达式

此外，`lambda`函数还可以与`map`, `sorted`等函数配合使用，我们以后再整理

list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
# [1, 4, 9, 16, 25, 36, 49, 64, 81]