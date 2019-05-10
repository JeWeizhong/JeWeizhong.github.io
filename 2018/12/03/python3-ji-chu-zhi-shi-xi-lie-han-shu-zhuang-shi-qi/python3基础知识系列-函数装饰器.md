---
title: python3基础知识系列--函数装饰器
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-12-03
mathjax: true

---

**装饰器**是一个函数，其主要用途是包装另一个函数或类。这种包装的首要目的是光明正大地修改或增强被包装对象的行为。语法上使用 特殊符号`@` 表示装饰器，如下所示：

```python
@trace
def square(x):
　　 return x*x
```
上面的代码是下面代码的简化：

```python
def square(x):
　　return x*x
square = trace(square)
```

下面介绍几种用法：


在类定义中，所有函数都被假定在实例上操作，该实例总是作为第一个参数self 传递。但是，还可以定义两种常见的方法。

**静态方法** 是一种普通函数，只不过它们正好位于类定义的命名空间中。它不会对任何实例类型进行操作。要定义静态方
法，可使用`@staticmethod` 装饰器，如下所示：

```python
class Foo(object):
　　 @staticmethod
　　 def add(x,y):
　　　　 return x + y
```

要调用静态方法，只需用类名作为它的前缀。无需向它传递任何其他信息，例如：

```python
x = Foo.add(3,4)　　 # x = 7
```

如果在编写类时需要采用很多不同的方式来创建新实例，则常常使用静态方法。因为类中只能有一个`__init__()` 函数，所以替代的创建函数通常按如下方式定义：

```python
class Date(object):
　　def __init__(self,year,month,day):
　　　　self.year = year
　　　　self.month = month
　　　　self.day = day
　　@staticmethod
　　def now():
　　　　 t = time.localtime()
　　　　 return Date(t.tm_year, t.tm_mon, t.tm_day)
　　@staticmethod
　　def tomorrow():
　　　　 t = time.localtime(time.time()+86400)
　　　　 return Date(t.tm_year, t.tm_mon, t.tm_day)
# 创建日期的示例
a = Date(1967, 4, 9)
b = Date.now()　　　　 # 调用静态方法now()
c = Date.tomorrow()　　# 调用静态方法tomorrow()
```

**类方法** 是将类本身作为对象进行操作的方法。类方法使用`@classmethod` 装饰器定义，与实例方法不同，因为根据约定，类是作为第一个参数（名为cls ）传递的，例如：

```python
class Times(object):
　　factor = 1
　　@classmethod
　　def mul(cls,x):
　　　　return cls.factor*x
class TwoTimes(Times):
　　factor = 2
x = TwoTimes.mul(4)　　# 调用Times.mul(TwoTimes, 4) -> 8
```

最后介绍一个装饰器函数是`@property`, 是将对象的方法变为属性的一个装饰器，这个在**python3cookbook**系列中讲过了，这里就不举例了