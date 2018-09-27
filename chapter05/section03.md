# WSGIRequest对象

`Django`在接收到`http`请求之后，会根据`http`请求携带的参数以及报文信息创建一个 `WSGIRequest `对象，并且作为视图函数第一个参数传给视图函数。也就是我们经常看到的 `request `参数。在这个对象上我们可以找到客户端上传上来的所有信息。这个对象的完整路径是 `django.core.handlers.wsgi.WSGIRequest` 。

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
9. `CONTENT_LENGTH`：请求的正文的长度（是一个字符串）。
10. `CONTENT_TYPE` ：请求的正文的MIME类型。
11. `HTTP_ACCEPT`：响应可接收的Content-Type。
12. `HTTP_ACCEPT_ENCODING`：响应可接收的编码。
13. `HTTP_ACCEPT_LANGUAGE`： 响应可接收的语言。
14. `HTTP_HOST`：客户端发送的HOST值。
15. `HTTP_REFERER`：在访问这个页面上一个页面的url。
16. `QUERY_STRING`：单个字符串形式的查询字符串（未解析过的形式）。
17. `REMOTE_ADDR`：客户端的IP地址。如果服务器使用了 nginx 做反向代理或者负载均衡，那么这个值返回的是 127.0.0.1 ，这时候可以使用 `HTTP_X_FORWARDED_FOR`来获取，所以获取 `ip`地址的代码片段如下：
```python
    if request.META.has_key('HTTP_X_FORWARDED_FOR'):
        ip = request.META['HTTP_X_FORWARDED_FOR']
    else:
        ip = request.META['REMOTE_ADDR']
```
18. `REMOTE_HOST`：客户端的主机名。
19. `REQUEST_METHOD`：请求方法。一个字符串类似于 `GET`或者 `POST`。
20. `SRVER_NAME `：服务器域名。
21. `SERVER_PORT`：服务器端口号，是一个字符串类型。