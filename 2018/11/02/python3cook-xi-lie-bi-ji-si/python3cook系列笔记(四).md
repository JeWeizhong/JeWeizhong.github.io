---
title: python3 cookbook笔记：第二章 字符串和文本
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-02
mathjax: true

---

本章介绍了几种字符串的操作方式，python提供了大量的内置的操作函数，若果还解决不了问题就需要用正则表达式。

由于自己对这部分处理没有需求，就只是大概看了看，而且后面几个小节没有看懂，因此这部分就暂时不整理了。

等以后有文字处理这方面的需求再回过头来复习


`startswith()` 和`endswith()` 方法提供了一个非常方便的方式去做字符串开头和结尾的检查,这里就不举例了。

**正则表达式**

    ```python
        re.compile() # 编译匹配模式
        re.match() # 匹配开头
        re.search() # 任意位置,但只匹配一次
        re.findall() # 匹配多次，返回元组
        re.finditer() # 匹配多次，返回迭代器
    ```
