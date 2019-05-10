---
title: python3 标准库系列(二)---argparse 模块
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-07
mathjax: true

---



`argparse` 模块是标准库中最大的模块之一，拥有大量的配置选项，本章只列出了一些常用的选项：

```python
import argparse

parser = argparse.ArgumentParser(description='Search some files')

parser.add_argument(dest='filenames',metavar='filename', nargs='*')

parser.add_argument('-p', '--pat',metavar='pattern', required=True,dest='patterns', action='append',	help='text pattern to search for')

parser.add_argument('-v', dest='verbose', action='store_true',help='verbose mode')

parser.add_argument('-o', dest='outfile', action='store',help='output file')
parser.add_argument('--speed', dest='speed', action='store',choices={'slow','fast'}, default='slow',help='search speed')

args = parser.parse_args()
# 使用方法
print(args.filenames)
print(args.patterns)
print(args.verbose)
print(args.outfile)
print(args.speed)

```

为了解析命令行选项， 你首先要创建一个`ArgumentParser` 实例，并使用`add_argument() `方法声明你想要支持的选项。

在每个`add_argument()` 调用中，

- `dest`参数指定解析结果被指派给属性的名字。

- `metavar` 参数被用来生成帮助信息(`metavar`仅更改显示的名称，`parse_args()`对象上属性的名称仍由`dest`值确定)。

- `action`参数指定跟属性对应的处理逻辑，通常的值为`store` , 被用来存储某个值或将多个参数值收集到一个列表中。其他参数：`store_true`,`store_false`:

```python
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action='store_true')
>>> parser.add_argument('--bar', action='store_false')
>>> parser.add_argument('--baz', action='store_false')
>>> parser.parse_args('--foo --bar'.split())
Namespace(foo=True, bar=False, baz=True)
```

下面的参数收集所有剩余的命令行参数到一个列表中。在本例中它被用来构造一个文件名列表：

```python
parser.add_argument(dest='filenames',metavar='filename', nargs='*') 
```

下面的参数根据参数是否存在来设置一个Boolean 标志：

```python
parser.add_argument('-v', dest='verbose', action='store_true',help='verbose mode')
```

下面的参数接受一个单独值并将其存储为一个字符串：

```python
parser.add_argument('-o', dest='outfile', action='store',help='output file')
```
- 如果要接收多个值，可以使用`nargs`参数，其支持的值有：

    - N  (整数), 检测命令行的该参数值的个数，只接收N个值
    - \* , 也就是说参数可以接收任意个数值
    - +,  与‘*’ 一样，但是如果少于一个值，会生成错误信息，有点像通配符
    - ？，如果传入了参数就使用参数给定的值，如果没有，就用默认值,这个需要设置默认值
    - `argparse.REMAINDER`所有剩余的命令行参数都收集到一个列表中:

```python
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('--foo')
>>> parser.add_argument('command')
>>> parser.add_argument('args', nargs=argparse.REMAINDER)
>>> print(parser.parse_args('--foo B cmd --arg1 XX ZZ'.split()))
Namespace(args=['--arg1', 'XX', 'ZZ'], command='cmd', foo='B')
```

- 下面的参数说明接受一个值，但是会将其和可能的选择值做比较，以检测其合法性：

```python
parser.add_argument('--speed', dest='speed', action='store',choices={'slow','fast'}, default='slow',help='search speed')
```

也就是说只能选择`choices`参数中的值，否则会报错

其他一些参数：

- `default`: 设置默认值
- `type`: 设置接收的内置类型，如`float`或`int`,(常见的内置类型和**函数**可以直接用作type参数的值)

```python
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('foo', type=int)
>>> parser.add_argument('bar', type=open)
>>> parser.parse_args('2 temp.txt'.split())
Namespace(bar=<_io.TextIOWrapper name='temp.txt' encoding='UTF-8'>, foo=2)
```

- `required`: 设置为必须参数


`ArgumentParser.add_argument_group`可以参数设置分组：

```python
>>> parser = argparse.ArgumentParser(prog='PROG', add_help=False)
>>> group = parser.add_argument_group('group')
>>> group.add_argument('--foo', help='foo help')
>>> group.add_argument('bar', help='bar help')
>>> parser.print_help()
usage: PROG [--foo FOO] bar

group:
  bar    bar help
  --foo FOO  foo help
```

```python
>>> parser = argparse.ArgumentParser(prog='PROG', add_help=False)
>>> group1 = parser.add_argument_group('group1', 'group1 description')
>>> group1.add_argument('foo', help='foo help')
>>> group2 = parser.add_argument_group('group2', 'group2 description')
>>> group2.add_argument('--bar', help='bar help')
>>> parser.print_help()
usage: PROG [--bar BAR] foo

group1:
  group1 description

  foo    foo help

group2:
  group2 description

  --bar BAR  bar help
```


- `ArgumentParser.add_mutually_exclusive_group(required=False)` 可以设置一组互斥的参数组，即在该组中参数只能选零个或一个,当`required=True`时，标准该参数组必须选组中的一个参数：

```python
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> group = parser.add_mutually_exclusive_group(required=True)
>>> group.add_argument('--foo', action='store_true')
>>> group.add_argument('--bar', action='store_false')
>>> parser.parse_args([]) 
# 如果required=False,那么这里不会报错
# Namespace(bar=True, foo=False)
usage: PROG [-h] (--foo | --bar)
PROG: error: one of the arguments --foo --bar is required
```