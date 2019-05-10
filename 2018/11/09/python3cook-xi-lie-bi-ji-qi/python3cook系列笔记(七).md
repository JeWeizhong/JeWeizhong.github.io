---
title: python3 cookbook笔记： 第八章 类与对象(一)
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-09
mathjax: true

---


## 类

类 定义了一组属性，这些属性与一组叫做**实例**的对象相关且由其共享。类通常是由函数（称为**方法** ，method）、变量（称为**类变量** ，class variable）和计算出的属性（称为**特性** ，property）组成的集合

### class 语句

使用`class`语句可定义类。类主体包含一系列在类定义时执行的语句，例如：

```python
class Account(object):
　　 num_accounts = 0
　　 def __init__(self,name,balance):
　　　　 self.name = name
　　　　 self.balance = balance
　　　　 Account.num_accounts += 1
　　 def __del__(self):
　　　　 Account.num_accounts -= 1
　　 def deposit(self,amt):
　　　　 self.balance = self.balance + amt
　　 def withdraw(self,amt):
　　　　 self.balance = self.balance - amt
　　 def inquiry(self):
　　　　 return self.balance
```

在类主体执行期间创建的值放在类对象中，这个对象充当着命名空间，与模块极为相似。例如，访问Account 类成员的方式如下：

```python
Account.num_accounts
Account.__init__
Account.__del__
Account.deposit
Account.withdraw
Account.inquiry
```

需要注意的是，class 语句本身并不创建该类的任何实例（例如，上一个例子实际上不会创建任何账户）。类仅设置将在以后创建的所有实例使用的属性。从这种意义上讲，可以将类看作一个蓝图。

类中定义的函数称为**实例方法** 。实例方法是一种在类的实例上进行操作的函数，类实例作为第一个参数传递。根据约定，这个参数称为`self` ，尽管所有合法的标识符都可以使用。在前面的例子中，`deposit()` 、`withdraw()`和`inquiry()` 都是实例方法。

**类变量**（如`num_accounts` ）是可在类的所有实例之间共享的值（也就是说，它们不是单独分配给每个实例的）。上例中的`num_accounts` 变量用于跟踪存在多少个Account 实例。

### 类实例

类的实例是以函数形式调用类对象来创建的。这种方法将创建一个新实例，然后将该实例传递给类的`__init__() `方法。`__init__()` 方法的参数包括新创建的实例self 和在调用类对象时提供的参数。例如：

```python
# 创建一些账户
a = Account("Guido", 1000.00)　 
# 调用Account.__init__(a,"Guido",1000.00)
b = Account("Bill", 10.00)
```
在`__init__()` 内，通过将属性分配给self 来将其保存到实例中。上例中，`self.name =  "Guido"`。

**上面创建了两个实例**，然后用(`.`)运算符即可访问属性：

```python
a.deposit(100.00)　　　
# 调用Account.deposit(a,100.00)
# self.balance已经通过__init__实例化,即self.balance = 1000.00
# 所以访问deposit属性后：
# self.balance = 1000.00 + 100.00
b.withdraw(50.00)　　　# 调用Account.withdraw(b,50.00)
name = a.name　　　　　# 获取账户名
```
## 对象属性

- 默认的字符串显示是`__str__()`方法，我们可以自定义`__repr__()` 和`__str__()`来是的交互更加友好
- `format()` 函数实际上是调用的`__formait__()`方法
- `with`语句是调用的`__enter__()` 和`__exit__()` 方法,我们可以在定义类的时候定义这两个方法，就能使对象支持`witg`上下文管理
  

  ```python
    from socket import socket, AF_INET, SOCK_STREAM
    
    class LazyConnection:
        def __init__(self, address, family=AF_INET, type=SOCK_STREAM):
            self.address = address
            self.family = family
            self.type = type
            self.sock = None
        def __enter__(self):
            if self.sock is not None:
                raise RuntimeError('Already connected')
            self.sock = socket(self.family, self.type)
            self.sock.connect(self.address)
            return self.sock
        def __exit__(self, exc_ty, exc_val, tb):
            self.sock.close()
            self.sock = None
  ```

- 类添加`__slots__`属性来极大的减少实例所占的内存,但使用后我们不能再给实例添加新的属性了，只能使用在`__slots__` 中定义的那些属性名。
- 约定是任何以单下划线`_` 开头的名字都应该是内部实现
- 使用双下划线开始会导致访问名称变成其他形式:

    ```python
    class B:
        def __init__(self):
            self.__private = 0
        def __private_method(self):
            pass
        def public_method(self):
            pass
            self.__private_method()
    ```

比如，在前面的类`B` 中，私有属性会被分别重命名为`_B__private` 和`_B__private_method` 。这时候你可能会问这样重命名的目的是什么，答案就是继承——这种属性通过继承是无法被覆盖的。比如：

    ```python
    class C(B):
        def __init__(self):
            super().__init__()
            self.__private = 1 # Does not override B.__private
        # Does not override B.__private_method()
        def __private_method(self):
        pass
    ```

这里，私有名称`__private` 和`__private_method` 被重命名为`_C__private` 和`_C__private_method` ，这个跟父类`B` 中的名称是完全不同的。

- 有时候你定义的一个变量和某个保留关键字冲突，这时候可以使用单下划线作为后缀