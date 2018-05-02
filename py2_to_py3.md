### Python2工程迁移至Python3的注意事项

#### 标准库的修改
1. `Python3`中将`Python2`中的`urllib2`、`urlparse`合并到`urllib`中，并修改了`urllib`模块。
  * `urllib.error`
  ```python3
  from urllib.error import HTTPError, URLError
  ```
  * `urllib.parse`
  ```python3
  from urllib.parse import urlparse, urljoin, urlencode, quote
  ```
  * `urllib.request`
  ```python3
  from urllib.request import urlopen, Request, build_opener
  ```

2. `Python3`中将`Python2`的`httplib`改为`htttp.client`
  ```python3
  from http.client import HTTPConnection
  ```

3. `Python3`中将`Python2`的`cookielib`改为`http.cookiejar'
  ```python3
  import http.cookiejar
  ```

#### print函数
  Python2
  ```python2
  print 'hello world!'  # hello world!
  ```
  `Python3`中将`print`视为函数
  ```python3
  print('hello, world!')  # hello, world!
  ```

#### try...except
  在`Python2`中
  ```python2
  try:
      ...
  except Exception, e:
      ...
  ```
  在`Python3`中改为
  ```python3
  try:
      ...
  except Exception as e:
      ...
  ```

#### 整除
  Python2
  ```python2
  3 / 2  # 1
  3 // 2  # 1
  3 / 2.0  # 1.5
  3 // 2.0  # 1.0
  ```

  Python
  ```python3
  3 / 2  # 1.5
  3 // 2  # 1
  3 / 2.0  # 1.5
  3 // 2.0  # 1.0
  ```

#### 编码
  `Python2`默认编码为ASCII，编写代码出现中文时需要在第一行加上#coding=utf-8
  ```python2
  import sys
  sys.getdefaultencoding()  # 'ascii'
  ```

  `Python3`默认编码为`UTF-8`, 删除unicode方法
  ```python3
  import sys
  sys.getdefaultencoding()  # 'utf-8'
  ```

#### 导入
  `Python3`默认使用绝对导入
  ```python3
  # 同目录导入
  from .file import *
  from directory.file import *

  from . import file

  # 从其他目录导入
  from directory1.directory2.file import *

  from directory1.directory2 import file
  ```

#### xrange
  `Python3`中取消`range`并将xrange`更改为`range`
  ```python3
  for i in range(x):
    ...

  l = list(range(x))
  ```

#### 字典
  `Python3`取消`dict.iterkeys(), dict.itervalues(), dick.iteritems()`
  将`dict.keys(), dict.values(), dict.items()`改为视图对象, 可迭代
  ```Python3
  type(dict.keys())  # <class 'dict_keys'>
  type(dict.items())  # <class 'dict_items'>
  type(dict.values())  # <class 'dict_values'>

  # 可迭代
  for key in dict.keys():
    print(keys)
  
  # 转为列表形式
  list(dict.keys())

#### long
  `Python3`中将`long`修改为`int`, 无论长短皆为int
  ```python3
  int(str)
  int(float)
  ```

#### list.sort(), sorted()
  `Python3`中对内置方法`sorted()`和`list.sort()`取消了对`cmp`参数的支持
  ```python3

  '''
  sorted(iterable, key=None, reverse=False)`
  key接受一个函数，这个函数只接受一个元素，默认为None, 一般配合lambda使用
  reverse是一个布尔值。如果设置为True，列表元素将被倒序排列，默认为False
  '''

  L = [1, 2, 5, 3, 4]
  
  # sorted()成新列表排序
  print(sorted(L))  # [1, 2, 3, 4, 5]
  print(L)  # [1, 2, 5, 3, 4]

  # sort()对原列表排序
  L.sort()
  print(L)  # [1, 2, 3, 4, 5]

