---
title: python3 cookbook笔记： 第八章 类与对象(二)
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-12
mathjax: true

---

*本节介绍property装饰器的用法*

`proprety`的作用是可以将方法变成属性，那它到底是怎么实现的呢？让我们先来看一个例子：

```python
class Person:
	def __init__(self, name):
		self.first_name = name
	# Getter 方法
	@property
	def first_name(self):
		return self._first_name
	# Setter 方法
	@first_name.setter
	def first_name(self, value):
		if not isinstance(value, str):
			raise TypeError('Expected a string')
		self._first_name = value
	# Deleter 方法(可选)
	@first_name.deleter
	def first_name(self):
		raise AttributeError("Can't delete attribute")		
```

使用方法：

```python
>>> a = Person('Guido')
>>> a.first_name # 调用getter方法
'Guido'
```
当我们进行赋值操作时`a = Person('Guido')`就创建了实例，在构造函数中的赋值语句`self.first_name`就会自动调用`first_name`的`getter`、`setter`、`deleter`方法(实质上是调用了`__get__()` 、`__set__()`和`__delete__()` 方法)。

而将等号右边的`name`参数传入到`@first_name.setter`下面的方法中，书中是用的`first_name`变量名字，其实这里设置成其他的变量名比较好，要不然容易混淆参数与方法函数。

因此装饰器`property`将原来的`first_name()`方法，变成了`self.first_name`属性,一开始我们要调用方法是`var.mothed()`,现在变成了属性，只用`.`运算符就可以, 括号就不需要了

接下来我们来看另一中形式：

```python
class Animal(object):
    def __init__(self, name, age):
        self._name = name

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        if isinstance(value, str):
            self._name = value
        else:
            self._name = 'to getter '

```

这里在构造函数中并没有调用`getter`、`setter`方法，那么，`self._name`，`self.name`其实是两个不同的属性，只是`self._name`属性依赖于`self.name`:

```python
>>> a = Animal('black dog', 3)
>>> a.name # 此时其实是没有调用setter方法的，只调用了 getter方法
'black dog'
>>> a._name # 因为没有调用setter方法，所以self._name值也没有修改
'black dog'
>>> a.name = 1 # 调用了setter方法，但是不是字符串类型的，所以执行 self._name = 'to getter '，然后调用getter方法，返回self._name的值
>>> a.name
'to getter '
>>> a._name # 因为进行了赋值操作，因此self._name的值也进行了修改
'to getter '
>>> a.name = "white55k" 
>>> a.name
'white55k'
>>> a._name
'white55k'
```

这里要说明的一点是`__init__`函数的参数`name`只是执行了`self._name = name`赋值操作，由于并没有调用`setter`方法，因此`def name(self, value)`需要重新传入参数(赋值)

在看另一种用法，只设置`getter`方法，变成只读属性，实现编程接口：

```python
class Student(object):

    @property
    def birth(self):
        return self._birth

    @birth.setter
    def birth(self, value):
        self._birth = value

    @property
    def age(self):
        return 2018 - self._birth
    
# 使用
>>> a=Student() # 创建实例
>>> a.birth=1990 # setter
>>> a.birth # getter
1990
>>> a.age # 只有 getter
28
>>> a.age=2 # 调用setter方法，会报错
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: can't set attribute
```