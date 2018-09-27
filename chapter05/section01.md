# Django限制请求method

## 常用的请求method

1. GET请求：GET请求一般用来向服务器索取数据，但不会向服务器提交数据，不会对服务器的状态进行更改。比如向服务器获取某篇文章的详情。
2. POST请求：POST请求一般是用来向服务器提交数据，会对服务器的状态进行更改。比如提交一篇文章给服务器。

## 限制请求装饰器

1. `Django `内置的视图装饰器可以给视图提供一些限制。比如这个视图只能通过 `GET `的 `method `访问等。以下将介绍一些常用的内置视图装饰器。
```python
    from django.views.decorators.http import require_http_methods
    
    @require_http_methods(["GET"])
    def my_view(request):
        pass
```
2. `django.views.decorators.http.require_GET` ：这个装饰器相当于是 `require_http_methods(['GET'])` 的简写形式，只允许使用 `GET `的 `method `来访问视图。示例代码如下：
```python
    from django.views.decorators.http import require_GET
    
    @require_GET
    def my_view(request):
        pass
```
3. `django.views.decorators.http.require_POST` ：这个装饰器相当于是 `require_http_methods(['POST'])` 的简写形式，只允许使用 `POST `的 `method `来访问视图。示例代码如下：
```python
    from django.views.decorators.http import require_POST
    
    @require_POST
    def my_view(request):
        pass
```