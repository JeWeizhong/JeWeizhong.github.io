---
title: python3 标准库系列(六)---base64
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-30
mathjax: true

---

Base64是一种用64个字符来表示任意二进制数据的方法。

```python
# 将二进制转为64位 存储
base64.b64encode(b'hello world')
```




    b'aGVsbG8gd29ybGQ='




```python
base64.b64decode(b'aGVsbG8gd29ybGQ=') # 解码
```




    b'hello world'




```python
base64.b64decode(b'aGVsbG8gd29ybGQ=').decode("utf-8")
```




    'hello world'

