---
title: python3 标准库系列(五)---subprocess
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-14
mathjax: true

---

subprocess模块是用来执行外部命令，下面介绍两种主要的用法。

## `subprocess.run`

 这里`call`, `check_call`, `check_output`, 与其功能性相似，就不展开介绍了

常用参数：

```python
subprocess.run (args，*,
				stdin = None,
				input = None,
				stdout = None,
				stderr = None,
				shell = False,
				cwd = None,
				timeout = None,
				check = False,
				encoding = None,
				errors = None,
				env = None )
```
默认不会捕获stdout或stderr

如果check为true，并且进程以非零退出代码退出， CalledProcessError则会引发异常。该异常的属性包含参数，退出代码以及stdout和stderr（如果它们被捕获）,

```python
# 这里捕获了stdout,stderr
>>> a = subprocess.run(["ls", "-l", "/dev/null"],stdout=subprocess.PIPE,stderr=subprocess.PIPE)
>>> a.stdout
b'crw-rw-rw-. 1 root root 1, 3 8\xe6\x9c\x88   3 13:24 /dev/null\n'
>>> a.stdout
b'crw-rw-rw-. 1 root root 1, 3 8\xe6\x9c\x88   3 13:24 /dev/null\n'
>>> a.stdout.decode("utf-8")
```

如果我们使用了check参数的话，如果执行错误，就会引发异常，这里什么都捕获不到

```python
>>> b = subprocess.run(["ls", "-l", "/null"], check=True,stdout=subprocess.PIPE,stderr=subprocess.PIPE)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/share/chg2master/prod/Other/zhongjw/miniconda3/lib/python3.6/subprocess.py", line 418, in run
    output=stdout, stderr=stderr)
subprocess.CalledProcessError: Command '['ls', '-l', '/null']' returned non-zero exit status 2.
>>> dir(b)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'b' is not defined
```

*python3.7里新增了`capture_output`参数,如果设置为true，则将捕获stdout和stderr。 使用时，将使用stdout = PIPE和stderr = PIPE自动创建内部Popen对象。 也可能不使用stdout和stderr参数。那么Popen对象的stdout、stderr与上面示例里的有什么不同呢？*

##`subprocess.Popen`

subprocess中更底层的进程创建和管理可以通过Popen类实现。它提供了更多的灵活性，程序员通过它能处理更多复杂的情况。

常用参数：

```python
subprocess.Popen(args, 
				bufsize=-1, 
				executable=None, 
				stdin=None, 
				stdout=None, 
				stderr=None, 
				preexec_fn=None, 
				close_fds=True, 
				shell=False, 
				cwd=None, 
				env=None, 
				universal_newlines=False, 
				startupinfo=None, 
				creationflags=0, 
				restore_signals=True, 
				start_new_session=False, 
				pass_fds=(), *, 
				encoding=None, 
				errors=None)
```
示例：

```python
>>> obj = subprocess.Popen(["ls", "-l", "/null"], stdout=subprocess.PIPE, stderr=subprocess.PIPE)
>>> obj.stderr.read().decode("utf-8")
'ls: 无法访问/null: 没有那个文件或目录\n'
>>> obj.stderr.read().decode("utf-8")

```
从上面示例中可以看出Popen对象的stdout、stderr获取的是数据流，而run捕获的stdout、stderr是字符串格式

当然官方文档说是不建议使用`.stdin.write`,` .stdout.read` 或者 `.stderr.read `，而是用`.communicate`方法，该方法会造成阻塞，以防止其他进程读取数据流造成死锁

`.communicate()`方法如果同时有` .stdout`和 `.stderr`, 返回的是一个元组, 用下标可以访问捕获的`stdout`和 `stderr`。

还要补充一点就是如果不捕获输出的话，是不会造成阻塞的，如果要等待子进程运行完可以使用`.wait`方法，而`subprocesee.run`方法会造成主程序的阻塞，因为要获取子进程运行状态。

Popen对象的主要方法有：

```python
['communicate', 'kill', 'pid', 'poll', 'returncode', 'send_signal', 'stderr', 'stdin', 'stdout', 'terminate', 'universal_newlines', 'wait']
```

如果有需要使用其他的参数和方法请查阅[官方文档](https://docs.python.org/3.6/library/subprocess.html#module-subprocess)

