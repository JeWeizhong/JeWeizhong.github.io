---
title: 用python3写的cgi服务器脚本无法显示中文
categories: 
  - python3
tags:
  - 编程语言
  - python3
date: 2018-11-28
---


在脚本前加上如下两句即可正常显示中文

```python
import codecs, sys
sys.stdout = codecs.getwriter('utf8')(sys.stdout.buffer)
```
脚本示例：

```python
#!/miniconda3/bin/python

import codecs, sys
sys.stdout = codecs.getwriter('utf8')(sys.stdout.buffer)

print ("Content-type:text/html")
print ()
print ('<html>')
print ('<head>')
print ('<meta charset="utf-8">')
print ('<title>Hello Word</title>')
print ('</head>')
print ('<body>')
print ('<h2>Hello Word!中文测试</h2>')
print ('</body>')
print ('</html>')
```