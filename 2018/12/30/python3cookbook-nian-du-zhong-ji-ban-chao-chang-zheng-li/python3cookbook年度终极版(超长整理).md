---

title: python3 cookbook年度终极版(超长整理)
categories:
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-12-30
mathjax: true

---



## 写在开头

<u>这次是对《python3 cookbook》的最终整理笔记，只是一个简短的对问题的回答，方便以后查阅，但其中的原理和扩展知识说的不是很详细，毕竟篇幅有限，所以还是需要去查资料、看文档。鉴于自己也是刚入门python，对书中的一些问题看的不是很明白，因此就出现了很多带有 “略”、“待补充的”字样，如果以后有机会接触到类似的问题，或者自己突然开窍就明白了其中的一些原理，会继续回来补充的</u>

---

---

## 第一章：数据结构和算法

### 1.1 解压序列赋值给多个变量

**Q:现在有一个包含N 个元素的元组或者是序列，怎样将它里面的值解压后同时赋值给N 个变量**


A: 元祖, 列表，或者其他可迭代对象都可以进行直接解压，并赋值，用 `_`,` *_` 占位并丢弃，

用 `*name`  可以解压成列表形式

```python
>>> data = [ 'ACME', 50, 91.1, (2012, 12, 21) ]
>>> _, shares, price, _ = data
>>> shares
50
>>> price
91.1
>>>
```

### 1.2 解压可迭代对象赋值给多个变量

**Q: 如果一个可迭代对象的元素个数超过变量个数时，会抛出一个ValueError 。那么怎样才能从这个可迭代对象中解压出N 个元素出来？**

A: 星号表达式可以用来收集那些不确定的变量:

```python
>>> *trailing, current = [10, 8, 7, 1, 9, 5, 10, 3]
>>> trailing
[10, 8, 7, 1, 9, 5, 10]
>>> current
3
```

### 1.3 保留最后N 个元素

**Q: 在迭代操作或者其他操作的时候，怎样只保留最后有限几个元素的历史记录？**

A: `collections.deque`会构造函数会新建一个固定大小的队列。当新的元素加入并
且这个队列已满的时候，最老的元素会自动被移除掉。


```python
>>> from collections import deque
>>> q = deque(maxlen=3)
>>> q.append(1)
>>> q.append(2)
>>> q.append(3)
>>> q
deque([1, 2, 3], maxlen=3)
>>> q.append(4)
>>> q
deque([2, 3, 4], maxlen=3)
>>> q.append(5)
>>> q
deque([3, 4, 5], maxlen=3)
>>>
```
*`collections.deque`构造的队列也支持在队列两头进行插入删除操作*

### 1.4 查找最大或最小的N 个元素

**Q: 怎样从一个集合中获得最大或者最小的N 个元素列表？**

A: `heapq` 模块有两个函数：`nlargest()` 和`nsmallest()` 可以完美解决这个问题。

```python
import heapq
nums = [1, 8, 2, 23, 7, -4, 18, 23, 42, 37, 2]
print(heapq.nlargest(3, nums)) # Prints [42, 37, 23]
print(heapq.nsmallest(3, nums)) # Prints [-4, 1, 2]
```

也可以接收关键字参数：

```python
portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
cheap = heapq.nsmallest(3, portfolio, key=lambda s: s['price'])
expensive = heapq.nlargest(3, portfolio, key=lambda s: s['price'])

# 上面代码在对每个元素进行对比的时候，会以price 的值进行比较
```

### 1.5 实现一个优先级队列

**Q:怎样实现一个按优先级排序的队列？并且在这个队列上面每次pop 操作总是返回优先级最高的那个元素**

A: 利用heapq 模块可是实现一个简单的优先级队列:

```python
import heapq

class PriorityQueue:
    def __init__(self):
        self._queue = []
        self._index = 0
    def push(self, item, priority):
        heapq.heappush(self._queue, (-priority, self._index, item))
        self._index += 1
    def pop(self):
        return heapq.heappop(self._queue)[-1]
```

使用方式：

```python
>>> class Item:
...     def __init__(self, name):
...         self.name = name
...     def __repr__(self):
...         return 'Item({!r})'.format(self.name)
...
>>> q = PriorityQueue()
>>> q.push(Item('foo'), 1)
>>> q.push(Item('bar'), 5)
>>> q.push(Item('spam'), 4)
>>> q.push(Item('grok'), 1)
>>> q.pop()
Item('bar')
>>> q.pop()
Item('spam')
>>> q.pop()
Item('foo')
>>> q.pop()
Item('grok')
>>>
```

### 1.6 字典中的键映射多个值

**Q: 怎样实现一个键对应多个值的字典（也叫multidict）？**

A:这其实也就是字典与列表的嵌套，有三种方式实现： 一是自己创建，二是用`collections.defaultdict`，最后一种是用`setdefault` : 

```python
d = {}
for key, value in pairs:
    if key not in d:
        d[key] = []
    d[key].append(value)
```

`defaultdict` 会自动为将要访问的键（就算目前字典中并不存在这样的键）创建映射实体:


```python
from collections import defaultdict

d = defaultdict(list)
for key, value in pairs:
    d[key].append(value)
```

```python
d = {} # A regular dictionary
d.setdefault('a', []).append(1)
d.setdefault('a', []).append(2)
d.setdefault('b', []).append(4)
```

### 1.7 字典排序

**Q: 你想创建一个字典，并且在迭代或序列化这个字典的时候能够控制元素的顺序**

A: 字典默认是不排序的，所以每次遍历输出的键顺序是不一样的，而`collections.OrderedDict`会创建一个键顺序固定的字典：

```python
from collections import OrderedDict

d = OrderedDict()
d['foo'] = 1
d['bar'] = 2
d['spam'] = 3
d['grok'] = 4
# Outputs "foo 1", "bar 2", "spam 3", "grok 4"
for key in d:
    print(key, d[key])`
```

### 1.8 字典的运算

**Q: 怎样在数据字典中执行一些计算操作（比如求最小值、最大值、排序等等）？**

A: `zip()`函数可以将字典的键与值反转，然后我们可以愉快的进行排序，求最值等操作了：

```python
>>> prices = {
... 'ACME': 45.23,
... 'AAPL': 612.78,
... 'IBM': 205.55,
... 'HPQ': 37.20,
... 'FB': 10.75
... }
>>>
>>> zip(prices.values(), prices.keys())
<zip object at 0x7fdfae678548>
>>> min(zip(prices.values(), prices.keys()))
(10.75, 'FB')
```

### 1.9 查找两字典的相同点

**Q: 怎样在两个字典中寻找相同点（比如相同的键、相同的值等等）？**

A: 其实就是两个列表的运算：

```python
>>> a = {
... 'x' : 1,
... 'y' : 2,
... 'z' : 3
... }
>>> b = {
... 'w' : 10,
... 'x' : 11,
... 'y' : 2
... }
>>> a.keys() & b.keys()
{'x', 'y'}
>>> a.keys() - b.keys()
{'z'}
>>> b.keys() - a.keys()
{'w'}
>>> a.items() & b.items()
{('y', 2)}
>>>
```

### 1.10 删除序列相同元素并保持顺序

**Q: 怎样在一个序列上面保持元素顺序的同时消除重复的值？**

A: 大家首先想到的方法是`ser()`，但是这种方法不能维护元素的顺序，其实方法有很多中，可以构建一个键位固定的字典(`OrderedDict`),也可对``set()稍作调整：

```python
def dedupe(items):
    seen = set()
    for item in items:
        if item not in seen:
        yield item
        seen.add(item)
```

### 1.11 命名切片

**Q: 你的程序已经出现一大堆已无法直视的硬编码切片下标，然后你想清理下代码。**

A: `slice()`方法可以对切片进行命名，而避免用一大堆难以阅读的下标进行运算：

```python
>>> items = [0, 1, 2, 3, 4, 5, 6]
>>> a = slice(2, 4)
>>> items[2:4]
[2, 3]
```

### 1.12 序列中出现次数最多的元素

**Q:怎样找出序列中出现次数最多的元素呢？**

A: `collecttions.Counter`类中的`most_common`就可以直接给出答案，当然你也可以手动计数：

```python
>>> a = [1,1,2,1,2,6,6,6,6]
>>> from
from
>>> from collections import Counter
>>> w = Counter(a)
>>> w
Counter({6: 4, 1: 3, 2: 2})
# 出现频率最高的1个数
>>> w.most_common(1)
[(6, 4)]
# 出现频率最高的两个数
>>> w.most_common(2)
[(6, 4), (1, 3)]
>>>
```

### 1.13 通过某个关键字排序一个字典列表

**Q:你有一个字典列表，你想根据某个或某届字典字段来排序这个列表**

A: `operator` 模块的`itemgetter` 函数可以生成一个**callable**对象，当作关键字传入到`sorted`函数中：

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

### 1.14 排序不支持原生比较的对象

**Q: 你想排序类型相同的对象，但是他们不支持原生的比较操作。**

A: 内置的`sorted`是值可以传入**callable**对象，传入其他的对象就需要用`operator.attrgetter()` 或者`lamba`来构造，示例略

### 1.15 通过某个字段将记录分组

**Q: 个字典或者实例的序列，然后你想根据某个特定的字段比如date 来分组迭代访问。**

A: 用`itertools.groupby()`函数, 使用之前要先排序，示例略


### 1.16 过滤序列元素

**Q: 个数据序列，想利用一些规则从中提取出需要的值或者是缩短序列**

A: 最简单的方法就是列表推倒，也可以直接迭代，这里要说的另外的方法就是`filter()`函数，它接收一个函数和要过滤的列表，接受的函数可以定义过滤规则，另外一个值得关注的过滤工具就是`tertools.compress()`, 用法也差不多：

```python
values = ['1', '2', '-3', '-', '4', 'N/A', '5']

def is_int(val):
    try:
        x = int(val)
        return True
    except ValueError:
        return False
        
ivals = list(filter(is_int, values))
print(ivals)
# Outputs ['1', '2', '-3', '4', '5']
```

### 1.17 从字典中提取子集

**Q:构造一个字典，它是另外一个字典的子集。**

A: 字典推倒，示例略

### 1.18 映射名称到序列元素

**Q: 段通过下标访问列表或者元组中元素的代码，但是这样有时候会使得你的代码难以阅读，于是你想通过名称来访问元素。**

A:  使用`collections.namedtuple()`函数来对元组进行命名：

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

### 1.19 转换并同时计算数据

**Q: 在数据序列上执行聚集函数（比如sum() , min() , max() ），但是首先你需
要先转换或者过滤数据**


A: 一般来讲，我们都是先进行过滤再进行计算，但是python为了是语法更简洁，可以允许我们省略中间的临时列表：

```python
nums = [1,2,3,4,5]
s = sum((x * x for x in nums)) # 显示的传递一个生成器表达式对象
s = sum(x * x for x in nums) # 更加优雅的实现方式，省略了括号
```


### 1.20 合并多个字典或映射

**Q: 多个字典或者映射，你想将它们从逻辑上合并为一个单一的映射后执行某些操作，比如查找值或者检查某些键是否存在。**

A: `llections`模块中的`CainMap`类可以讲两个字典在逻辑上和为一个字典：

```python

a = {'x': 1, 'z': 3 }
b = {'y': 2, 'z': 4 }

from collections import ChainMap

c = ChainMap(a,b)
print(c['x']) # Outputs 1 (from a)
print(c['y']) # Outputs 2 (from b)
print(c['z']) # Outputs 3 (from a)  若有重复值那么只输出一个的值
```

---

---

## 第二章：字符串和文本

### 2.1 使用多个界定符分割字符串

**Q: 你需要将一个字符串分割为多个字段，但是分隔符(还有周围的空格) 并不是固定的。**

A: 用`re.split`, 因为`str.split`并不允许多个分隔符：

```python
>>> line = 'asdf fjdk; afed, fjek,asdf, foo'
>>> import re
>>> re.split(r'[;,\s]\s*', line)
['asdf', 'fjdk', 'afed', 'fjek', 'asdf', 'foo']
# 如果有捕获分组的话，如果捕获到了也会输出
>>> fields = re.split(r'(;|,|\s)\s*', line)
>>> fields
['asdf', ' ', 'fjdk', ';', 'afed', ',', 'fjek', ',', 'asdf', ',', 'foo']
>>>
# 形如(?:....) 就不会输出捕获的分组
>>> re.split(r'(?:,|;|\s)\s*', line)
['asdf', 'fjdk', 'afed', 'fjek', 'asdf', 'foo']
>>>
```

### 2.2 字符串开头或结尾匹配

**Q: 你需要通过指定的文本模式去检查字符串的开头或者结尾，比如文件名后缀，URLScheme 等等。**

A: 参考`startswith()` 和`endswith()` 方法,当然也可以用正则表达式或者对字符串进行切片

```python
>>> filename = 'spam.txt'
>>> filename.endswith('.txt')
True
>>> filename.startswith('file:')
False
>>> url = 'http://www.python.org'
>>> url.startswith('http:')
True
>>>
```
也可以接收以元祖作为参数

### 2.3 用Shell 通配符匹配字符串

**A: 你想使用Unix Shell 中常用的通配符(比如`*.py` , `Dat[0-9]*.csv` 等) 去匹配文本字符串**

A: 参见`fnmatch` 模块提供的两个函数——`fnmatch()` 和`fnmatchcase()`，示例略

### 2.4 字符串匹配和搜索

**Q: 你想匹配或者搜索特定模式的文本**

A: 正则表达式


### 2.5 字符串搜索和替换

**Q: 你想在字符串中搜索和匹配指定的文本模式**

A: `str.replace()` ,`re.sub()`:

```python
>>> text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
>>> import re
>>> re.sub(r'(\d+)/(\d+)/(\d+)', r'\3-\1-\2', text)
'Today is 2012-11-27. PyCon starts 2013-3-13.'
>>>
```

`sub()` 函数中的第一个参数是被匹配的模式，第二个参数是替换模式。反斜杠数字比如"\3" 指向前面模式的**捕获组号**。

### 2.6 字符串忽略大小写的搜索替换

**Q: 你需要以忽略大小写的方式搜索与替换文本字符串**

A: 在正则表达式中使用`re.IGNORECASE`参数

### 2.7 最短匹配模式

**Q: 你正在试着用正则表达式匹配某个文本模式，但是它找到的是模式的最长可能匹配。而你想修改它变成查找最短的可能匹配。**

A: 使用`?`进行非贪婪匹配：

```python
>>> text2 = 'Computer says "no." Phone says "yes."'
>>> str_pat.findall(text2)
['no." Phone says "yes.']
# 非贪婪匹配
>>> str_pat = re.compile(r'\"(.*?)\"')
>>> str_pat.findall(text2)
['no.', 'yes.']
>>>
```

### 2.8 多行匹配模式

**Q: 你正在试着使用正则表达式去匹配一大块的文本，而你需要跨越多行去匹配。**

A: 使用`re.DOTALL`参数：

```python
>>> import re
>>> a = '''hello
... world'''
>>> a
'hello\nworld'
>>> re.search('(o.*)',a,re.DOTALL).group(1)
'o\nworld'
>>>
```

### 2.9 将Unicode 文本标准化

**Q: 你正在处理Unicode 字符串，需要确保所有字符串在底层有相同的表示。**

A: 在Unicode 中，某些字符能够用多个合法的编码表示,所以有些字符在编码字节是不同的，但是在显示的时候却是一样的，这种情况可以用`unicodedata` 模块先将文本标准化

```python
>>> s1 = 'Spicy Jalape\u00f1o'
>>> s2 = 'Spicy Jalapen\u0303o'
>>> s1
'Spicy Jalapeño'
>>> s2
'Spicy Jalapeño'
>>> s1 == s2
False
####
>>> import unicodedata
>>> t1 = unicodedata.normalize('NFC', s1)
>>> t2 = unicodedata.normalize('NFC', s2)
>>> t1 == t2
True
```

关于`unicodedata` 模块的其他用法请参考官方文档

### 2.10 在正则式中使用Unicode

**Q:你正在使用正则表达式处理文本，但是关注的是Unicode 字符处理。**

A: `re`模块支持Unicode 字符，直接用就好：

```python
>>> pat = re.compile('stra\u00dfe', re.IGNORECASE)
>>> s = 'straße'
>>> pat.match(s) # Matches
<_sre.SRE_Match object at 0x10069d370>
>>> pat.match(s.upper()) # Doesn't match
>>> s.upper() # Case folds
'STRASSE'
>>>
```

### 2.11 删除字符串中不需要的字符

**Q: 你想去掉文本字符串开头，结尾或者中间不想要的字符，比如空白**

A: `strip()` 方法能用于删除开始或结尾的字符。`lstrip()` 和`rstrip()` 分别从左和从右执行删除操作。默认情况下，这些方法会去除空白字符，但是你也可以指定其他字符。
当然也可以使用正则表达式

### 2.12 审查清理文本字符串

**Q: 一些无聊的幼稚黑客在你的网站页面表单中输入文本”pýtĥöñ”，然后你想将这些字符清理掉。**

A: 解决的思路就是用`str.upper()` 和`str.lower()`将字符串标准化，使用`str.replace()` 或者`re.sub()`
对字符串进行删除或者替换，问题中的文本 "pýtĥöñ" 则可以用`unicodedata.normalize()` 函数将unicode文本标准化。

```python
>>> a
'pýtĥöñ is awesome\n'
>>> b = unicodedata.normalize('NFD', a)
>>> b.encode('ascii', 'ignore').decode('ascii')
'python is awesome\n'
>>>
```

另外再介绍一下经常会被忽视的`str.translate()`方法:

```python
>>> s = 'pýtĥöñ\fis\tawesome\r\n'
>>> s
'pýtĥöñ\x0cis\tawesome\r\n'
>>> remap = {
... ord('\t') : ' ', # 将特殊字符重新映射为空格
... ord('\f') : ' ',
... ord('\r') : None # 删除
... }
>>> a = s.translate(remap)
>>> a
'pýtĥöñ is awesome\n'
>>>
```

### 2.13 字符串对齐

**Q: 你想通过某种对齐方式来格式化字符串**

A: 可以使用字符串的`ljust()` , `rjust()` 和`center()`

```python
>>> text = 'Hello World'
# 默认是空格，也可以接收其他字符
>>> text.ljust(20)
' Hello World            '
>>> text.rjust(20)
'            Hello World '
>>> text.center(20)
'       Hello World      '
>>>
```

还有就是`format()`函数：

```python
# > <　＾　分别是居左，居右，居中
>>> format(text, '>20')
'       Hello World'
＃　也可以在字符串格式化中用
>>> '{:>10s} {:>10s}'.format('Hello', 'World')
'    Hello　　 World '
```
最后，老代码中的％操作符来格式化字符串就不细说啦

### 2.14 合并拼接字符串

**Q: 你想将几个小的字符串合并为一个大的字符串**

A: `' '.join(str)` 和"+"操作符都可以合并字符串，比较简单就不举例了


### 2.15 字符串中插入变量

**Q: 你想创建一个内嵌变量的字符串，变量被它的值所表示的字符串替换掉。**

A: 这里推荐python3.6版本的新特性: **f-string** :

```python
>>> name = "Lee"
>>> n = 666
>>> s = f'{name} has {n} messages.'
>>> s
'Lee has 666 messages.'
>>>
```

### 2.16 以指定列宽格式化字符串

**Q: 你有一些长字符串，想以指定的列宽将它们重新格式化。**

A: 使用`textwrap` 模块来格式化字符串的输出:

```python
>>> import textwrap
>>> print(textwrap.fill(s, 70)) # 宽度70
>>> print(textwrap.fill(s, 40, initial_indent='  ')) # 宽度40，首行空两格
>>> print(textwrap.fill(s, 40, subsequent_indent='  ')) # 宽度40，非首行空两格
```

### 2.17 在字符串中处理html 和xml

**Q: 你想将HTML 或者XML 实体如&entity; 或&#code; 替换为对应的文本。再者，你需要转换文本中特定的字符(比如<, >, 或&)。**

A: 使用`html.escape()`


### 2.18 字符串令牌解析

**Q: 你有一个字符串，想从左至右将其解析为一个令牌流。**

A: 看不懂题目

### 2.19 实现一个简单的递归下降分析器

**Q: 你想根据一组语法规则解析文本并执行命令，或者构造一个代表输入的抽象语法树。如果语法非常简单，你可以自己写这个解析器，而不是使用一些框架。**

A: 看不懂答案

### 2.20 字节字符串上的字符串操作

**Q: 你想在字节字符串上执行普通的文本操作(比如移除，搜索和替换)。**

A: 与普通文本字符串的操作没啥差别，需要注意的是使用正则表达式的时候要加`b`:

```python
>>> data = b'FOO:BAR,SPAM'
>>> import re
>>> re.split(b'[:,]', data)
```

---

---

## 第三章：数字日期和时间

### 3.1 数字的四舍五入


**Q:你想对浮点数执行指定精度的舍入运算**

A: 简单的用`round(value, ndigits)` 函数, 也可以格式化

### 3.2 执行精确的浮点数运算

**Q: 你需要对浮点数执行精确的计算操作，并且不希望有任何小误差的出现。**

A: 科学计算用`decimal` 模块

### 3.3 数字的格式化输出

**Q: 你需要将数字格式化后输出，并控制数字的位数、对齐、千位分隔符和其他的细节。**

A: 跟字符串格式化输出一样，使用`format`函数

### 3.4 二八十六进制整数

**Q: 你需要转换或者输出使用二进制，八进制或十六进制表示的整数。**

A: 了将整数转换为二进制、八进制或十六进制的文本串，可以分别使用`bin()` ,`oct()` 或`hex()` 函数：

```python
>>> x = 1234
>>> bin(x)
'0b10011010010'
>>> oct(x)
'0o2322'
>>> hex(x)
'0x4d2'
>>>
```

### 3.5 字节到大整数的打包与解包


**Q: 你有一个字节字符串并想将它解压成一个整数。或者，你需要将一个大整数转换为一个字节字符串。**

A: 字节转整数用`int.from_bytes()`,整数转字节用`int.to_bytes()`


### 3.6 复数的数学运算(略)

### 3.7 无穷大与NaN(略)

### 3.8 分数运算(略)

### 3.9 大型数组运算

**Q: 你需要在大数据集(比如数组或网格) 上面执行计算。**

A: 请参阅numpy和pandas库

### 3.10 矩阵与线性代数运算(略)

### 3.11 随机选择

**Q: 你想从一个序列中随机抽取若干元素，或者想生成几个随机数。**

A: 请使用`random`模块，除了生成随机数随机序列，还可以生成均匀分布、高斯分布等其他函数，随机数的生成也可以设置初始化种子(`random.seed()`)

### 3.12 基本的日期与时间转换

**Q:你需要执行简单的时间转换，比如天到秒，小时到分钟等的转换。**

A: 日期与时间处理用`datetime`模块

### 3.13 计算最后一个周五的日期(略)

### 3.14 计算当前月份的日期范围(略)

### 3.15 字符串转换为日期

**Q: 你的应用程序接受字符串格式的输入，但是你想将它们转换为datetime 对象以便在上面执行非字符串操作。**

A:  一个简单的时间格式化问题：

```python
>>> from datetime import datetime
>>> t = '2012-09-20'
>>> y = datetime.strptime(t,'%Y-%m-%d')
>>> z = datetime.now()
>>> y
datetime.datetime(2012, 9, 20, 0, 0)
>>> z
datetime.datetime(2018, 12, 19, 22, 1, 9, 914504)
>>> z-y
datetime.timedelta(2281, 79269, 914504)
>>>
```

---

---

## 第四章：迭代器与生成器

### 4.1 手动遍历迭代器

**Q: 你想遍历一个可迭代对象中的所有元素，但是却不想使用for 循环。**


A: 使用`next()`函数来获取下一个对象：

```python
>>> list = [1,2,3,4,5,6]
>>> a = iter(list) # 转换成可迭代对象才可以使用next方法
>>> next(a)
1
>>> next(a)
2
>>> next(a)
3
>>> next(a)
4
```

### 4.2 代理迭代

**Q: 你构建了一个自定义容器对象，里面包含有列表、元组或其他可迭代对象。你想直接在你的这个新容器对象上执行迭代操作。**

A: 给对象定义一个`__iter__`方法就好：

```python
def __iter__(self) :
    return iter(self.items)
```

### 4.3 使用生成器创建新的迭代模式

**Q: 你想实现一个自定义迭代模式，跟普通的内置函数比如`range()` , `reversed()` 不一样。**

A: `yield`就是一个生成器函数，生成一个迭代对象

### 4.4 实现迭代器协议

**Q: 你想构建一个能支持迭代操作的自定义对象，并希望找到一个能实现迭代协议的简单方法。**

A: 其实就是想自己定义迭代的方法，那就定义一个`__next__`方法就好


### 4.5 反向迭代

**Q: 你想反方向迭代一个序列**

A: 用`reversed()`函数，~~先有韦神后有天，反向迭代耗时间~~


### 4.6 带有外部状态的生成器函数

**Q: 你想定义一个生成器函数，但是它会调用某个你想暴露给用户使用的外部状态值。**

A: 如果你想让你的生成器暴露外部状态给用户，别忘了你可以简单的将它实现为一
个类，然后把生成器函数放到`__iter__()` 方法中过去

### 4.7 迭代器切片

**Q: 你想得到一个由迭代器生成的切片对象，但是标准切片操作并不能做到。**

A: 函数`itertools.islice()`: 

```python
>>> def count(n):
... while True:
... yield n
... n += 1
...
>>> c = count(0)
>>> c[10:20] # 会报错
>>> import itertools
>>> for x in itertools.islice(c, 10, 20):
... print(x)
...
10 # 一直到20 
```

### 4.8 跳过可迭代对象的开始部分

**Q: 你想遍历一个可迭代对象，但是它开始的某些元素你并不感兴趣，想跳过它们。**

A: `itertools.dropwhile()`函数可以做到，比如跳过文件开头几行的注释：

```python
>>> from itertools import dropwhile
>>> with open('/etc/passwd') as f:
... for line in dropwhile(lambda line: line.startswith('#'), f):
... print(line, end='')
...
nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
root:*:0:0:System Administrator:/var/root:/bin/sh
...
>>>
```

### 4.9 排列组合的迭代

**Q: 你想迭代遍历一个集合中元素的所有可能的排列或组合**

A: 去看一下`itertools.permutations()`,`itertools.combinations()`, `itertools.combinations_with_replacement`使用方法就好了


### 4.10 序列上索引值迭代

**Q:你想在迭代一个序列的同时跟踪正在被处理的元素索引。**

A: 这个简单，用`enumerate()`

### 4.11 同时迭代多个序列

**Q: 你想同时迭代多个序列，每次分别从一个序列中取一个元素**

A: 章口就莱：`zip()`


### 4.12 不同集合上元素的迭代

**Q: 你想在多个对象执行相同的操作，但是这些对象在不同的容器中，你希望代码在不失可读性的情况下避免写重复的循环。**

A: 其实你把这个两个对象合在一起就好了，但是这样做就会有多余的赋值操作，所以就可以用`itertools.chain()`函数了：

```python
>>> from itertools import chain
>>> a = [1, 2, 3, 4]
>>> b = ['x', 'y', 'z']
>>> for x in chain(a, b): # 等价于 for x in a+b
... print(x)
...
1
2
3
4
x
y
z
>>>
```

### 4.13 创建数据处理管道


**Q: 你想以数据管道(类似Unix 管道) 的方式迭代处理数据。比如，你有个大量的数据需要处理，但是不能将它们一次性放入内存中**

A: 这个还是看书吧，书中的例子将的比较详细，注意一下`yiled`和`yield from`用法


### 4.14 展开嵌套的序列

**Q: 你想将一个多层嵌套的序列展开成一个单层列表**

A: 详见`yield from` 用法


### 4.15 顺序迭代合并后的排序迭代对象



**Q: 你有一系列排序序列，想将它们合并后得到一个排序序列并在上面迭代遍历。**

A: `heapq.merge()` 函数可以帮你解决这个问题


### 4.16 迭代器代替while 无限循环

**Q: 你在代码中使用while 循环来迭代处理数据，因为它需要调用某个函数或者和一般迭代模式不同的测试条件。能不能用迭代器来重写这个循环呢？**

A: 可以用`iter()`函数转成可迭代对象，然后就可以用for循环啦


---

---

## 第五章：文件与IO

### 5.1 读写文本数据

**Q: 你需要读写各种不同编码的文本数据，比如ASCII，UTF-8 或UTF-16 编码等。**

A: `open`函数的`encoding`参数可以解决这个问题


### 5.2 打印输出至文件中

**Q: 你想将print() 函数的输出重定向到一个文件中去。**

A: `print`函数有个`file`参数可以指定文件


### 5.3 使用其他分隔符或行终止符打印

**Q: 你想使用print() 函数输出数据，但是想改变默认的分隔符或者行尾符。**

A: `print()` 函数中使用`sep` 和`end` 关键字参数


### 5.4 读写字节数据

**Q: 你想读写二进制文件，比如图片，声音文件等等。**

A: `open`函数的`rb`,`wb`可以读写二进制文件


### 5.5 文件不存在才能写入

**Q: 你想像一个文件中写入数据，但是前提必须是这个文件在文件系统上不存在。也就是不允许覆盖已存在的文件内容。**

A: `w`模式默认会清空文件，那就换成`x`模式就好

### 5.6 字符串的I/O 操作

**Q: 你想使用操作类文件对象的程序来操作文本或二进制字符串。**

A: 使用`io.StringIO()`和`io.BytesIO`类：

```python
>>> s = io.StringIO()
>>> s.write('Hello World\n')
12
>>> print('This is a test', file=s)
15
>>> # Get all of the data written so far
>>> s.getvalue() # 注意此时文件指针是指向末尾的，所以你用s.read()是读不出来的
'Hello World\nThis is a test\n'
>>>
>>> # Wrap a file interface around an existing string
>>> s = io.StringIO('Hello\nWorld\n') # 指针在开始出
>>> s.read(4)
'Hell'
>>> s.read()
'o\nWorld\n'
>>>
```

### 5.7 读写压缩文件

**Q: 你想读写一个gzip 或bz2 格式的压缩文件。**

A: 用`gzip`和`bz2`模块的`open`函数


### 5.8 固定大小记录的文件迭代

**Q: 你想在一个固定长度记录或者数据块的集合上迭代，而不是在一个文件中一行一行的迭代。**

A: 方法倒是有，不过我不觉得有必要这样做：

```python
from functools import partial
RECORD_SIZE = 32
with open('somefile.data', 'rb') as f:
    records = iter(partial(f.read, RECORD_SIZE), b'')
    for r in records:
        ...#　输出
```

### 5.9 读取二进制数据到可变缓冲区中

**Q: 你想直接读取二进制数据到一个可变缓冲区中，而不需要做任何的中间复制操作。或者你想原地修改数据并将它写回到一个文件中去。**

A: `readinto()`函数就是将文件数据读取到一个数组中去：

```python
import os.path
def read_into_buffer(filename):
buf = bytearray(os.path.getsize(filename)) # 此时的buf是一个二进制的数组
with open(filename, 'rb') as f:
f.readinto(buf)
return buf
```
和普通`read()` 方法不同的是，`readinto()` 填充已存在的缓冲区而不是为新对象重新分配内存再返回它们


### 5.10 内存映射的二进制文件

**Q: 你想内存映射一个二进制文件到一个可变字节数组中，目的可能是为了随机访问它的内容或者是原地做些修改。**

A: `mmap `模块可以解决这个问题，有需要的去看[官方文档](https://docs.python.org/3/library/mmap.html)吧，这里就不介绍了


### 5.11 文件路径名的操作

**Q: 你需要使用路径名来获取文件名，目录名，绝对路径等等。**

A: `os.path`模块就不用我多说了吧


### 5.12 测试文件是否存在

**Q: 你想测试一个文件或目录是否存在。**

A: `os.path.isfile()`, `os.path.isdir()`, `os.path.islink()` , `os.path.realpath()`, `os.path.abspath`, `os.path.getsize()`...


### 5.13 获取文件夹中的文件列表

**Q: 你想获取文件系统中某个目录下的所有文件列表。**

A: `os.listdir()`

### 5.14 忽略文件名编码

**Q: 你想使用原始文件名执行文件的I/O 操作，也就是说文件名并没有经过系统默认编码去解码或编码过。**

A: 既然文件名编码不知道，那就统一用字节名吧，然后用字节名当做文件名读写：

```python
with open(b'jalapen\xcc\x83o.txt') as f:
```

### 5.15 打印不合法的文件名


**Q: 你的程序获取了一个目录中的文件名列表，但是当它试着去打印文件名的时候程序崩溃，出现了UnicodeEncodeError 异常和一条奇怪的消息——surrogates notallowed 。**

A: 解决方法：`repr(filename)[1:-1]`这样就将一些奇奇怪怪的字正确显示


### 5.16 增加或改变已打开文件的编码

**Q: 你想在不关闭一个已打开的文件前提下增加或改变它的Unicode 编码。**

A: 我不建议这么做

### 5.17 将字节写入文本文件

**Q: 你想在文本模式打开的文件中写入原始的字节数据。**

A: 将字节数据直接写入文件的缓冲区即可：

```python
import sys`
sys.stdout.buffer.write(b'Hello\n')
```

### 5.18 将文件描述符包装成文件对象

**Q: 你有一个对应于操作系统上一个已打开的I/O 通道(比如文件、管道、套接字等)的整型文件描述符，你想将它包装成一个更高层的Python 文件对象**

A: 包装文件描述符的技术可以很方便的将一个类文件接口作用于一个以不同方式打开的I/O 通道上，如管道、套接字等，有需要的自己去查资料吧


### 5.19 创建临时文件和文件夹

**Q: 你需要在程序执行时创建一个临时文件或目录，并希望使用完之后可以自动销毁掉。**

A: `tempfile` 模块的`TemporaryFile() `、`NamedTemporaryFile()` 和`TemporaryDirectory()` 函数
可以满足需求

### 5.20 与串行端口的数据通信

**Q: 你想通过串行端口读写数据，典型场景就是和一些硬件设备打交道(比如一个机器人或传感器)。**

A: 请使用[`pySerial `](https://pyserial.readthedocs.io/en/latest/)


### 　5.21 序列化Python 对象

**Q: 你需要将一个Python 对象序列化为一个字节流，以便将它保存到一个文件、存储到数据库或者通过网络传输它。**

A: 请使用`pickle.load`, `pickle.loads`, `pickle.dump`, `pickle.dumps`



---

---

## 第六章：数据编码和处理


### 6.1 读写CSV 数据

**Q: 你想读写一个CSV 格式的文件。**

A: 请看示例：

```python
import csv

with open('stocks.csv') as f:
    f_csv = csv.reader(f)
    for row in f_csv:
        # 其他操作
        # 请注意这里的row是一个列表，是自动以逗号分隔的
```

### 6.2 读写JSON 数据

**Q: 你想读写JSON(JavaScript Object Notation) 编码格式的数据。**

A: `json.loads`读取JSON格式，`json.dumps`转换为JSON格式，对应的文件读写方法是：`json.dump`, `json.load`

### 6.3 解析简单的XML 数据

**Q: 你想从一个简单的XML 文档中提取数据。**

A: 可以试一下`xml.etree.ElementTree`模块的`parse`函数

### 6.4 增量式解析大型XML 文件

**Q: 你想使用尽可能少的内存从一个超大的XML 文档中提取数据。**

A: 任何时候只要你遇到增量式的数据处理时，第一时间就应该想到迭代器和生成器。示例略

### 6.5 将字典转换为XML(略)

### 6.6 解析和修改XML(略)

### 6.7 利用命名空间解析XML 文档(略)

### 6.8 与关系型数据库的交互

**Q: 你想在关系型数据库中查询、增加或删除记录**

A: 可以使用内置的`sqlite3 模块`， 安装其他模块也可以

### 6.9 编码和解码十六进制数

**Q: 你想将一个十六进制字符串解码成一个字节字符串或者将一个字节字符串编码成一个十六进制字符串。**

A: `base64.b16decode()` 和`base64.b16encode()`

### 6.10 编码解码Base64 数据

**Q: 你需要使用Base64 格式解码或编码二进制数据。**

A: `base64.b64decode()` 和`base64.b64encode()`

### 6.11 读写二进制数组数据(略)

### 6.12 读取嵌套和可变长二进制数据(略)

### 6.13 数据的累加与统计操作(略)

---

---

## 第七章：函数

### 7.1 可接受任意数量参数的函数

**Q: 你想构造一个可接受任意数量参数的函数。**

A: 使用`*`任意位置参数和`**`任意关键字参数

### 7.2 只接受关键字参数的函数

**Q: 你希望函数的某些参数强制使用关键字参数传递**

A: 将强制关键字参数放到某个`*` 参数或者单个`*` 后面就能达到这种效果。比如：

```python
# 强制最后一个参数必须输入
def recv(maxsize, *, block):
    pass
```

### 7.3 给函数参数增加元信息

**Q: 你写好了一个函数，然后想为这个函数的参数增加一些额外的信息，这样的话其他使用者就能清楚的知道这个函数应该怎么使用。**

A: 使用函数注解：

```python
def add(x:int, y:int) -> int:
    return x + y
```
### 7.4 返回多个值的函数

**Q: 你希望构造一个可以返回多个值的函数**

A: `return`

### 7.5 定义有默认参数的函数

**Q: 你想定义一个函数或者方法，它的一个或多个参数是可选的并且有一个默认值。**

A:　这么简单就不回答了

### 7.6 定义匿名或内联函数

**你想为sort() 操作创建一个很短的回调函数，但又不想用def 去写一个单行函数，而是希望通过某个快捷方式以内联方式来创建这个函数。**

A: `lambda`表达式，但是只能指定单个表达式，它的值就是最后的返回值。


### 7.7 匿名函数捕获变量值

**Q: 你用lambda 定义了一个匿名函数，并想在定义时捕获到某些变量的值。**

A: 这个问题看似很难懂，其实你只要明白下列代码的意思就懂了：

```python 
a = lambda y: x + y
a = lambda y, x=x: x + y
###
funcs = [lambda x: x+n for n in range(5)]
funcs = [lambda x, n=n: x+n for n in range(5)]
```

### 7.8 减少可调用对象的参数个数(待补充)

### 7.9 将单方法的类转换为函数(待补充)

### 7.10 带额外状态信息的回调函数(待补充)

### 7.11 内联回调函数(待补充)

### 7.12 访问闭包中定义的变量(待补充)


---

---

## 第八章：类与对象

### 8.1 改变对象的字符串显示

**Q: 你想改变对象实例的打印或显示输出，让它们更具可读性。**

A: 重新定义`__str__() `和`__repr__()`方法就好

```python
class Pair:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    def __repr__(self):
        return 'Pair({0.x!r}, {0.y!r})'.format(self)
    def __str__(self):
        return '({0.x!s}, {0.y!s})'.format(self)
```
使用方法

```python
>>> p = Pair(3, 4)
>>> p
Pair(3, 4) # __repr__() output
>>> print(p)
(3, 4) # __str__() output
>>>
```

### 8.2 自定义字符串的格式化

**Q: 你想通过format() 函数和字符串方法使得一个对象能支持自定义的格式化。**

A:  重新定义`__format__()`方法

### 8.3 让对象支持上下文管理协议

**Q:你想让你的对象支持上下文管理协议(with 语句)。**

A:  让你的对象实现`__enter__()`和`__exit__()`方法

### 8.4 创建大量对象时节省内存方法

**Q: 你的程序要创建大量(可能上百万) 的对象，导致占用很大的内存。**

A: 给你的类添加`__slots__`把对象存入元组中

### 8.5 在类中封装属性名

**Q: 你想封装类的实例上面的“私有”数据，但是Python 语言并没有访问控制。**

A: 私有属性可以用单下划线的命名命名，若果以双下划线开头命名，那么访问方式会有所改变

### 8.6 创建可管理的属性

**Q: 你想给某个实例attribute 增加除访问与修改之外的其他处理逻辑，比如类型检查或合法性验证。**

A: `@property`装饰器可以设置getter 、setter 和deleter 方法

### 8.7 调用父类方法

**Q: 你想在子类中调用父类的某个已经被覆盖的方法。**

A: 在子类中使用`super()`函数

### 8.8 子类中扩展property

**Q: 在子类中，你想要扩展定义在父类中的property 的功能。**

A: 直接继承并修改父类中的方法

### 8.9 创建新的类或实例属性


**Q: 你想创建一个新的拥有一些额外功能的实例属性类型，比如类型检查。**

A: `@property`装饰器是对`__get__()`, `__set__()`, `__delete__()`的实现，如果不想用装饰器，那就这几个函数重新定义就好

### 8.10 使用延迟计算属性


**Q: 你想将一个只读属性定义成一个property，并且只在访问的时候才会计算结果。但是一旦被访问后，你希望结果值被缓存起来，不用每次都去计算。**

A: 只设置`@property.getter`


### 8.11 简化数据结构的初始化

**Q: 你写了很多仅仅用作数据结构的类，不想写太多烦人的__init__() 函数**


A: 可以在一个基类中写一个公用的`__init__()` 函数, 用`setattr()`实现


### 8.12 -8.25(待补充)

---

---

## 第九章：元编程(待补充)


---

---

## 第十章：模块与包

### 10.1 构建一个模块的层级包

**Q: 你想将你的代码组织成由很多分层模块构成的包。**

A: 封装成包是很简单的。在文件系统上组织你的代码，并确保每个目录都定义了一个`__init__.py` 文件

### 10.2 控制模块被全部导入的内容

**Q: 当使用’from module import \*’语句时，希望对从模块或包导出的符号进行精确控制**

A: 在你的模块中定义一个`__all__`属性来列出你要导出的内容

### 10.3 使用相对路径名导入包中子模块

**Q: 将代码组织成包, 想用import 语句从另一个包名没有硬编码过的包的中导入子模块。**

A: `from . import  samepackge` ,其实`from`后面跟一个相对路径就好

### 10.4 将模块分割成多个文件

**Q: 你想将一个模块分割成多个文件。但是你不想将分离的文件统一成一个逻辑模块时使已有的代码遭到破坏。**

A: 在`__init__.py` 文件中统一导入就好，如果模块太多又不想导入，也可以定义成延迟导入(详情见本书示例)

### 10.5 利用命名空间导入目录分散的代码

**Q: 你可能有大量的代码，由不同的人来分散地维护。每个部分被组织为文件目录，如一个包。然而，你希望能用共同的包前缀将所有组件连接起来，不是将每一个部分作为独立的包来安装。**

A: 用一个顶级的python包，包下面有不同的模块文件，删去用来将组件联合起来的`__init__.py` 文件

### 10.6 重新加载模块

**Q: 你想重新加载已经加载的模块，因为你对其源码进行了修改。**

A: 使用`imp.reload()`

### 10.7 运行目录或压缩文件

**Q: 您有一个已成长为包含多个文件的应用，它已远不再是一个简单的脚本，你想向用户提供一些简单的方法运行这个程序**

A: 在目录里添加`__main__.py`文件就可以把目录当做一个运行程序来运行啦

### 10.8 读取位于包中的数据文件

**Q: 你的包中包含代码需要去读取的数据文件。你需要尽可能地用最便捷的方式来做这件事**

A: 假设你的包中的文件组织成如下：

```python
mypackage/
    __init__.py
    somedata.dat
    spam.py
```
现在假设`spam.py` 文件需要读取`somedata.dat` 文件中的内容。你可以用以下代码来完成：

```python
# spam.py
import pkgutil
data = pkgutil.get_data(__package__, 'somedata.dat')
```

### 10.9 将文件夹加入到sys.path

**Q: 你无法导入你的Python 代码因为它所在的目录不在sys.path 里。你想将添加新目录到Python 路径，但是不想硬链接到你的代码。**

A: 第一种方式是在你的环境变量`PYTHONPATH`添加路径，第二种就是创建一个`myapplication.pth`文件将路径写进去，放在`site-packages`目录下，还有就是在脚本开头导入路径`sys.path.insert(0, '/some/dir')`，`sys.path.appedn('/some/dir')`，


### 10.10 通过字符串名导入模块


**Q: 你想导入一个模块，但是模块的名字在字符串里。你想对字符串调用导入命令。**

A: `importlib.import_module()`函数返回一个模块对象

### 10.11 通过钩子远程加载模块(待补充)

### 10.12 导入模块的同时修改模块(待补充)


### 10.13 安装私有的包

**Q: 你想要安装一个第三方包，但是没有权限将它安装到系统Python 库中去。或者，你可能想要安装一个供自己使用的包，而不是系统上面所有用户。**

A: 
```python
python3 setup.py install --user
pip install --user packagename
```

### 10.14 创建新的Python 环境

**Q: 你想创建一个新的Python 环境，用来安装模块和包。不过，你不想安装一个新的Python 克隆，也不想对系统Python 环境产生影响。**

A: 用`pyvenv`创建一个虚拟环境，请参阅[PEP405](https://www.python.org/dev/peps/pep-0405/)

### 10.15 分发包(待补充)

---

---

## 第十一章：网络与Web 编程(待补充)

### 11.1 作为客户端与HTTP 服务交互

**Q: 你需要通过HTTP 协议以客户端的方式访问多种服务。例如，下载数据或者与基于REST 的API 进行交互**

A: 通常我们会有`urllib.request`, `requests` 两种方式来我完成，由于`requests`库比较简单，所以示例我们这个来写个简单的POST请求：

```python
import requests
url = 'http://httpbin.org/post'
# Dictionary of query parameters (if any)
parms = {
    'name1' : 'value1',
    'name2' : 'value2'
}
# Extra headers
headers = {
    'User-agent' : 'none/ofyourbusiness',
    'Spam' : 'Eggs'
}
resp = requests.post(url, data=parms, headers=headers)
# Decoded text returned by the request
text = resp.text
```

---

---

## 第十二章：并发编程(待补充)

### 12.1 启动与停止线程

**Q: 你要为需要并发执行的代码创建/销毁线程**

A: `threading`库的简单使用：

```python 
from threading import Thread
t = Thread(target=somemethod,args=(someargvs,))
t.start()
t.join()
t.is_alive()
```

### 12.2 判断线程是否已经启动


**Q: 你已经启动了一个线程，但是你想知道它是不是真的已经开始运行了。**

A: 使用`threading` 库中的`Event` 对象

### 12.3 线程间通信

**Q: 你的程序中有多个线程，你需要在这些线程之间安全地交换信息或数据**

A: 基本思路就是搞个外部队列，然后各个线程进行get或者put数据

### 12.4 给关键部分加锁

**Q: 你需要对多线程程序中的临界区加锁以避免竞争条件。**

A: 注意一下`threading.RLock` 和 `threading.Lock` 的区别

### 12.5 防止死锁的加锁机制

**Q: 你正在写一个多线程程序，其中线程需要一次获取多个锁，此时如何避免死锁问题。**

A: 给锁加ID

### 12.6 保存线程的状态信息

**Q: 你需要保存正在运行线程的状态，这个状态对于其他的线程是不可见的。**

A: 

---

---

## 第十三章：脚本编程与系统管理

### 13.1 通过重定向/管道/文件接受输入

**Q: 13.1 通过重定向/管道/文件接受输入**

A: `fileinput.input()`可以接收管道输入的文件列表

### 13.2 终止程序并给出错误信息

**Q: 你想向标准错误打印一条消息并返回某个非零状态码来终止程序运行解**

A: 抛出一个`SystemExit` 异常:

```python
raise SystemExit('It failed!')
或者
import sys
sys.stderr.write('It failed!\n')
raise SystemExit(1)
```

### 13.3 解析命令行选项

**Q: 你的程序如何能够解析命令行选项（位于sys.argv 中）**

A: `argparse` 模块

### 13.4 运行时弹出密码输入提示

**Q: 你写了个脚本，运行时需要一个密码。此脚本是交互式的，因此不能将密码在脚本中硬编码，而是需要弹出一个密码输入提示，让用户自己输入。**

A:　看代码：

```python
import getpass
user = getpass.getuser()
passwd = getpass.getpass()
```

代码中`getpass.getuser()` 不会弹出用户名的输入提示。它会根据该用户的shell 环境或者会依据本地系统的密码库（支持pwd 模块的平台）来使用当前用户的登录名，如果你想显示的弹出用户名输入提示，使用内置的input 函数：
```python
user = input('Enter your username: ')
```

### 13.5 获取终端的大小

**Q: 你需要知道当前终端的大小以便正确的格式化输出。**

A: `os.get_terminal_size()`函数

### 13.6 执行外部命令并获取它的输出

**Q: 你想执行一个外部命令并以Python 字符串的形式获取执行结果。**

A: `subprocess`模块

### 13.7 复制或者移动文件和目录

**Q: 你想要复制或移动文件和目录，但是又不想调用shell 命令。**

A: `shutil` 模块

```python
import shutil
# Copy src to dst. (cp src dst)
shutil.copy(src, dst)
# Copy files, but preserve metadata (cp -p src dst)
shutil.copy2(src, dst)
# Copy directory tree (cp -R src dst)
shutil.copytree(src, dst)
# Move src to dst (mv src dst)
shutil.move(src, dst)
```

### 13.8 创建和解压归档文件

**Q: 你需要创建或解压常见格式的归档文件（比如.tar, .tgz 或.zip）**

A: `shutil` 模块拥有两个函数——`make_archive()` 和`unpack_archive()` 可派上用场。例如：
```python
>>> import shutil
>>> shutil.unpack_archive('Python-3.3.0.tgz')
>>> shutil.make_archive('py33','zip','Python-3.3.0')
```

### 13.9 通过文件名查找文件

**Q: 你需要写一个涉及到文件查找操作的脚本，比如对日志归档文件的重命名工具，你不想在Python 脚本中调用shell，或者你要实现一些shell 不能做的功能。**

A: 用`os.walk()` 函数, `os.walk()` 方法为我们遍历目录树，每次进入一个目录，它会返回一个三元组，包
含相对于查找目录的相对路径，一个该目录下的目录名列表，以及那个目录下面的文件名列表。

### 13.10 读取配置文件

**Q: 怎样读取普通.ini 格式的配置文件？**

A: `configparser` 模块

### 13.11 给简单脚本增加日志功能

**Q: 你希望在脚本和程序中将诊断信息写入日志文件。**

A: `logging` 模块

### 13.12 给函数库增加日志功能

**Q: 你想给某个函数库增加日志功能，但是又不能影响到那些不使用日志功能的程序。**

A: `getLogger(__name__)`是给当前模块添加日志，相应的把`__name__`换成其他模块就是给其他模块添加(修改)日志

### 13.13 实现一个计时器(略)

### 13.14 限制内存和CPU 的使用量

**Q: 你想对在Unix 系统上面运行的程序设置内存或CPU 的使用限制。**

A: `resource` 模块

### 13.15 启动一个WEB 浏览器

**Q: 你想通过脚本启动浏览器并打开指定的URL 网页**

A: `webbrowser` 模块

---

---

## 第十四章：测试、调试和异常(待补充)

---

---

## 第十五章：C 语言扩展(待补充)

