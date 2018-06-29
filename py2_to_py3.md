### Python2工程迁移至Python3的注意事项

#### 标准库的修改
1. `Python3`中将`Python2`中的`urllib2`、`urlparse`合并到`urllib`中，并修改了`urllib`模块。
* `urllib.error`
```python
from urllib.error import HTTPError, URLError
```
* `urllib.parse`
```python
from urllib.parse import urlparse, urljoin, urlencode, quote
```
* `urllib.request`
```python
from urllib.request import urlopen, Request, build_opener
```

2. `Python3`中将`Python2`的`httplib`改为`htttp.client`
```python
from http.client import HTTPConnection
```

3. `Python3`中将`Python2`的`cookielib`改为`http.cookiejar`
```python
import http.cookiejar
```

4. `python3`中取消`commands`，改用`subprocess`
[详细](https://www.cnblogs.com/zhming26/p/6283361.html)

#### print函数
`python2`
```python
print 'hello world!'  # hello world!
```
`Python3`中将`print`视为函数
```python
print('hello, world!')  # hello, world!
```

#### try...except
`python2`
```python
try:
    ...
except Exception, e:
    ...
```
`Python3`
```python
try:
    ...
except Exception as e:
    ...
```

#### 整除
`python2`
```python
3 / 2  # 1
3 // 2  # 1
3 / 2.0  # 1.5
3 // 2.0  # 1.0
```

`python3`
```python
3 / 2  # 1.5
3 // 2  # 1
3 / 2.0  # 1.5
3 // 2.0  # 1.0
```

#### 编码
`python2`默认编码为ASCII，编写代码出现中文时需要在第一行加上#coding=utf-8
```python
import sys
sys.getdefaultencoding()  # 'ascii'
```

`python3`默认编码为`UTF-8`, 删除unicode方法
```python
import sys
sys.getdefaultencoding()  # 'utf-8'
```

#### 导入
`python3`默认使用绝对导入
```python
# 同目录导入
from .file import *
from directory.file import *

from . import file

# 从其他目录导入
from directory1.directory2.file import *

from directory1.directory2 import file
```

#### xrange
`python3`中取消`range`并将xrange`更改为`range`
```python
for i in range(x):
  ...

l = list(range(x))
```

#### 字典
`Python3`取消`dict.iterkeys()`, `dict.itervalues()`, `dick.iteritems()`
`dict.keys()`, `dict.values()`, `dict.items()`改为视图对象, 可迭代
```python
type(dict.keys())  # <class 'dict_keys'>
type(dict.items())  # <class 'dict_items'>
type(dict.values())  # <class 'dict_values'>

# 可迭代
for key in dict.keys():
  print(keys)

# 转为列表形式
list(dict.keys())
```

#### long
`python3`中将`long`修改为`int`, 无论长短
```python
int(str)
int(float)
```

#### list.sort(), sorted()
`Python3`中对内置方法`sorted()`和`list.sort()`取消了对`cmp`参数的支持
```python

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
```

#### 新式8进制变量SynataxError: invalid token错误
`python2`
```
>>> 01
1
```

`python3`
```
>>> 01
SynataxError: invalid token
>>> 0o1
1
```

#### python3取消cmp
```python
'''python2中的cmp'''
cmp(80, 100)   # -1
cmp(180, 100)  #  1
cmp(-80, 100)  # -1
cmp(80, -100)  #  1

'''python3改用operator, 返回bool类型'''
import operator
operator.gt(1,2)      # greater than
operator.ge(1,2)      # greater and equal
operator.eq(1,2)      # equal
operator.le(1,2)      # less and equal
operator.lt(1,2)      # less than
```

#### python3 md5
```python
'''python2'''
# 函数
import hashlib
def md5(str):
    m = hashlib.md5()
    m.update(str)
    return m.hexdigest()
    
# 简洁
sign = hashlib.md5(str).hexdigest()

'''python3'''
# 函数
def md5(str):
    m = hashlib.md5()
    m.update(str.encode('utf-8'))
    return m.hexdigest()
    
# 简洁
sign = hashlib.md5(str.encode('utf-8')).hexdigest()

'''
关于hashlib.md5(str).digest()
下面以拼接'a', 'b'为例

python2中返回二进制样式的str
>>> a = hashlib.md5('a').digest()
>>> a
'\x0c\xc1u\xb9\xc0\xf1\xb6\xa81\xc3\x99\xe2iw&a'

>>> hashlib.md5(a + 'b').hexdigest()
'2f63866f41c2a2b0228714a21d5f75fe'

python3中直接返回二进制类型
>>> a = hashlib.md5('a'.encode('utf-8')).digest()
>>> a
b'\x0c\xc1u\xb9\xc0\xf1\xb6\xa81\xc3\x99\xe2iw&a'

>>> hashlib.md5((str(a) + 'b').encode('utf-8').hexdigest()
'80193715c7375e9cdd7c3d5867d5889c'

返回结果并不一致，解决方法如下
>>> c = b'%s%s' % (a, bytes('b'.encode('utf-8')))
>>> hashlib.md5(c).hexdigest()
'2f63866f41c2a2b0228714a21d5f75fe'
'''
```
