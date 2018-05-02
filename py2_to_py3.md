### Python2工程迁移至Python3的注意事项

#### 标准库的修改
1. urllib
  `Python3`中将`Python2`中的`urllib2`、`urlparse`合并到`urllib`中，并修改了`urllib`模块。
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
2. httplib
  `Python3`中将`Python2`的`httplib`改为`htttp.client`
  ```python3
  from http.client import HTTPConnection
  ```

3. cookielib
  `Python3`中将`Python2`的`cookielib`改为`http.cookiejar'
  ```python3
  import http.cookiejar
  ```
