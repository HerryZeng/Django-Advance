# WSGIRequest对象

`Django`在接收到`http`请求之后，会根据`http`请求携带的参数以及报文信息创建一个 `WSGIRequest`对象，并且作为视图函数第一个参数传给视图函数。也就是我们经常看到的 `request `参数。在这个对象上我们可以找到客户端上传上来的所有信息。这个对象的完整路径是 `django.core.handlers.wsgi.WSGIRequest` 。

## WSGIRequest对象常用属性和方法

### WSGIRequest对象常用属性

WSGIRequest 对象上大部分的属性都是只读的。因为这些属性是从客户端上传上来的，没必要做任何的修改。以下将对一些常用的属性进行讲解：
1. `path`：请求服务器的完整“路径”，但不包含域名和参数。比如 `http://www.baidu.com/xxx/yyy/` ，那么 `path `就是 `/xxx/yyy/` 。
2. `method`：代表当前请求的 `http `方法。比如是 `GET `还是 `POST `。
3. `GET`：一个 `django.http.request.QueryDict` 对象。操作起来类似于字典。这个属性中包含了所有以 `?xxx=xxx` 的方式上传上来的参数。
4. `POST`：也是一个 `django.http.request.QueryDict` 对象。这个属性中包含了所有以 POST 方式上传上来的参数。
5. `FILES`：也是一个 `django.http.request.QueryDict` 对象。这个属性中包含了所有上传的文件。
6. `COOKIES` ：一个标准的Python字典，包含所有的 `cookie`，键值对都是字符串类型。
7. `session`：一个类似于字典的对象。用来操作服务器的 `session`。
8. `META`：存储的客户端发送上来的所有 header 信息。
    +  `CONTENT_LENGTH`：请求的正文的长度（是一个字符串）。
    +  `CONTENT_TYPE` ：请求的正文的MIME类型。
    + `HTTP_ACCEPT`：响应可接收的Content-Type。
    + `HTTP_ACCEPT_ENCODING`：响应可接收的编码。
    + `HTTP_ACCEPT_LANGUAGE`： 响应可接收的语言。
    + `HTTP_HOST`：客户端发送的HOST值。
    + `HTTP_REFERER`：在访问这个页面上一个页面的url。
    + `QUERY_STRING`：单个字符串形式的查询字符串（未解析过的形式）。
    + `REMOTE_HOST`：客户端的主机名。
    + `REQUEST_METHOD`：请求方法。一个字符串类似于 `GET`或者 `POST`。
    + `SRVER_NAME `：服务器域名。
    + `SERVER_PORT`：服务器端口号，是一个字符串类型。
    + `REMOTE_ADDR`：客户端的IP地址。如果服务器使用了 nginx 做反向代理或者负载均衡，那么这个值返回的是 127.0.0.1 ，这时候可以使用 `HTTP_X_FORWARDED_FOR`来获取，所以获取 `ip`地址的代码片段如下:

```python
    if request.META.has_key('HTTP_X_FORWARDED_FOR'):
        ip = request.META['HTTP_X_FORWARDED_FOR']
    else:
        ip = request.META['REMOTE_ADDR']
```


### WSGIRequest对象常用方法

1. `is_secure()` ：是否是采用 `https `协议。
2. `is_ajax()` ：是否采用 `ajax `发送的请求。原理就是判断请求头中是否存在 `X-Requested-With:XMLHttpRequest` 。
3. `get_host()` ：服务器的域名。如果在访问的时候还有端口号，那么会加上端口号。比如 `www.baidu.com:9000` 。
4. `get_full_path()` ：返回完整的path。如果有查询字符串，还会加上查询字符串。比如 `/music/bands/?print=True` 。
5. `get_raw_uri()` ：获取请求的完整 `url `。

### QueryDict对象

我们平时用的 `request.GET` 和 `request.POST` 都是 `QueryDict`对象，这个对象继承自 `dict`，因此用法跟 `dict`相差无几。其中用得比较多的是 `get`方法和 `getlist`方法。
1. `get`方法：用来获取指定 `key`的值，如果没有这个 `key`，那么会返回 `None`。
2. `getlist`方法：如果浏览器上传上来的 `key`对应的值有多个，那么就需要通过这个方法获取。

在`HttpRequest`对象中，`GET`和`POST`属性都是一个`django.http.QueryDict`的实例。`request.POST`或`request.GET`的`QueryDict`都是不可变的，只读的。如果要修改它，需要使用`QueryDict.copy()`方法，获取一个拷贝，然后在这个拷贝上进行修改操作。

**方法**
`QueryDict`实现了Python字典数据业的所有标准方法，因为它是字典的子类。不同之处有：

1. QueryDict.init(query_string=None,mutable=False,encoding=None)
```python
>>> QueryDict('a=1&a=2&c=3)
<QueryDict: {'a':['1','2'],'c':['3']}>
```
如果需要实例化可以修改的对象，添加参数`mutable=True`。

2. classmethod QueryDict.fromkeys(itrable,value='',mutable=False,encoding=None)
Django1.11中的新功能。循环可迭代对象中每个元素作为键值，并赋予同样的值（来自`value`参数）
```python
>>> QueryDict.fromkeys(['a', 'a', 'b'], value='val')
<QueryDict: {'a': ['val', 'val'], 'b': ['val']}>
```

3. QueryDict.update(other_dict)
用新的QueryDict或字典更新当前QueryDict。类似dict.update()，但是追加内容，而不是更新并替换它们。 像这样：
```python
>>> q = QueryDict('a=1', mutable=True)
>>> q.update({'a': '2'})
>>> q.getlist('a')
['1', '2']
>>> q['a'] # returns the last
'2'
```

4. QueryDict.items()
类似`dict.items()`，如果有重复项目，返回最近的一个，而不是都返回：
```python
>>> q = QueryDict('a=1&a=2&a=3')
>>> q.items()
[('a', '3')]
```

5. QueryDict.values()
类似dict.values()，但是只返回最近的值。 像这样：
```python
>>> q = QueryDict('a=1&a=2&a=3')
>>> q.values()
['3']
```

6. QueryDict.copy()
使用`copy.deepcopy()`返回`QueryDict`对象的副本。 此副本是可变的！

7. QueryDict.getlist(key, default=None)
返回键对应的值列表。 如果该键不存在并且未提供默认值，则返回一个空列表。

8. QueryDict.setlist(key, list_)
为`list`设置给定的键。

9. QueryDict.appendlist(key, item)
将键追加到内部与键相关联的列表中。

10. QueryDict.setdefault(key, default=None)
类似`dict.setdefault()`，为某个键设置默认值。

11. QueryDict.setlistdefault(key, default_list=None)
类似setdefault()，除了它需要的是一个值的列表而不是单个值。

12. QueryDict.lists()
类似`items()`，只是它将其中的每个键的值作为列表放在一起。 像这样：
>>> q = QueryDict('a=1&a=2&a=3')
>>> q.lists()
[('a', ['1', '2', '3'])]

13. QueryDict.pop(key)
返回给定键的值的列表，并从QueryDict中移除该键。 如果键不存在，将引发KeyError。 像这样：
```python
>>> q = QueryDict('a=1&a=2&a=3', mutable=True)
>>> q.pop('a')
['1', '2', '3']
```

14. QueryDict.popitem()
删除`QueryDict`任意一个键，并返回二值元组，包含键和键的所有值的列表。在一个空的字典上调用时将引发`KeyError`。 像这样：
```python
>>> q = QueryDict('a=1&a=2&a=3', mutable=True)
>>> q.popitem()
('a', ['1', '2', '3'])
```

15. QueryDict.dict()
将`QueryDict`转换为Python的字典数据类型，并返回该字典。如果出现重复的键，则将所有的值打包成一个列表，最为新字典中键的值。
```python
>>> q = QueryDict('a=1&a=3&a=5')
>>> q.dict()
{'a': '5'}
```

16. QueryDict.urlencode(safe=None)
已url的编码格式返回数据字符串。 像这样：
```python
>>> q = QueryDict('a=2&b=3&b=5')
>>> q.urlencode()
'a=2&b=3&b=5'
```
使用safe参数传递不需要编码的字符。 像这样：
```python
>>> q = QueryDict(mutable=True)
>>> q['next'] = '/a&b/'
>>> q.urlencode(safe='/')
'next=/a%26b/'
```
