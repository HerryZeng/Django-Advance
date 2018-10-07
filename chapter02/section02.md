## URL分发器

### 视图

视图一般都写在`APP`的`views.py`中。并且视图的第一个参数永远都是`request`（一个`HttpRequest`）对象。这个对象存储了请求过来的所有信息，包括携带的参数以及一些头部信息等。在视图中，一般是完成逻辑相关的操作。比如这个请求是添加一扁博客，那么可以通过request来接收这些数据，然后存储数据库中，最后再把执行的结果返回浏览器。视图函数的返回结果必须是`HttpResponseBase`对象或子类的对象。示例如下：
```python
    form django.http import HttpResponse
    
    def book_list(request):
        return HttpResponse("书籍列表")
```

### URL映射

视图写完后，要与URL进行映射，也即用户在浏览器中输入什么`url`的时候可以请求到这个视图函数。在用户输入了某个`url`，请求到我们的网站的时候，`django`会从项目的`urls.py`文件中寻找对应的视图。在`urls.py`文件中有一个`urlpatterns`变量，以后`django`就会从这个变量中读取所有的匹配规则。匹配规则需要使用`django.urls.path`函数进行包裹，这个函数会根据传入的参数返回`URLPattern`或是`URLResolver`的对象。示例如下：
```python
    from django contrib import admin
    from django.urls import path
    from book imports views
    
    urlpatterns = [
        path('admin/',admin.site.urls),
        path('book/',views.book_list),
    ]
```

### URL中添加参数

有时候，`url`中包含了一些参数需要动态调整。比如简书某篇文章的详情页的`url`，是`https://www.jianshu.com/p/a5aab9c4978e`后面的`a5aab9c4978e`就是这篇文章的`id`，那么简书的文章详情页的`url`就可以写成`https://www.jianshu.com/p/<id>`，其中`id`就是文章的`id`。那么如何 `django`中实现这种需求呢。这时候我们可以在`path`函数中，使用尖括号的形式来定义一个参数。比如我们现在想要获取一本书籍的详细信息，那么应该在`url`中指定这个参数。示例如下：
```python
    from django.contrib import admin
    from django.urls import path
    from book import views
    
    urlpatterns = [
        path('admin/',admin.site.urls),
        path('book/',views.book_list),
        path('book/<book_id>',views.book_detail),
    ]
```
而在`views.py`中的代码如下：
```python
    def book_detail(request):
        text = "你输入的书籍的ID是: %s" % book_id
        return HttpResponse(text)
```
当然，也可以通过查询字符串的方式传递一个参数过去。示例代码如下：
```python
    urlpatterns = [
        path('admin/',admin.site.urls),
        path('book/',views.book_list),
        path('book/detail/',views.book_detail),
    ]
```
而在`views.py`中的代码如下：
```python
    def book_detail(request):
        book_id = request.GET.get("id")
        text = "你输入的书籍的ID是: %s" % book_id
        return HttpResponse(text)
```
以后在访问的时候就是通过`/book/detail/?id=1`即可将参数传递过去。

## URL中包含另外一个urls模块

在我们的项目中，不可能只有一个`APP`，如果把所有的`APP`的`views`中的视图都放在`urls.py`中进行映射，肯定会让代码显得非常乱。因此`django`给我们提供了一个方法，可以在`APP`内部包含自己的`url`匹配规则，而在项目的`urls.py`中再统一包含这个`APP`的`urls`。使用这个技术需要借助`include`函数。示例如下：
```python
    # first_project/urls.py
    
    from django.contrib import admin
    from django.urls import path,include
    
    urlpatterns = [
        path('admin/',admin.site.urls),
        path('book/',include("book.urls")),
    ]
```
在`urls.py`文件中把所有的和`book`这个`APP`相关的`url`都移动到`app/urls.py`中了，然后在`first_project/urls.py`中，通过`include`函数包含`book.urls`，以后在请求`book`相关的`url`的时候需要加上一个`book`的前缀。
```python
    # book/urls.py
    
    from django.urls import path
    from . imports views
    
    urlpatterns = [
        path('list/',views.book_list),
        path('detail/<book_id>',views.book_detail),
    ]
```
以后访问书的列表的`url`的时候，就通过`/book/list/`来访问，访问书籍详情页面的`url`的时候就通过`/book/detail/<id>/`来访问。

### path函数

`path`函数的定义为`path(route,view,name=None,kwargs=None)`。以下对几个参数进行详解。
1. `route`参数：`url`的匹配规则。这个参数中可以指定`url`中需要传递的参数，比如在访问文章详情的时候，可以传递一个`id`。传递参数的时候通过`<>`尖括号来进行指定的。并且在传递参数的时候，可以指定这个参数的数据类型，比如文章的`id`都是`int`类型，那么可以这们写`<int:id>`，以后匹配的时候，就只会匹配到`id`为`int`类型的`url`，而不会匹配其他的`url`，并且在视图函数中获取这个参数的时候，就已经被转换成一个`int`类型了。其中还有几种常用的类型：
    + str: 非空的字符串类型。默认的转换器，但不能包含斜杠(/)。
    + int: 匹配任意的零或正数的整形。到视图函数中就是一个int类型。
    + slug: 由英文中的横杠`-`，或下划线`_`连接英文字符或数字而成的字符串。
    + uuid: 匹配`uuid`字符串
    + path: 匹配非空的英文字符串，可以包括斜杠(/)。
2. `view`参数：可以为一个视图函数或是`类视图.as_view()`或是`django.urls.include()`函数的返回值。
3. `name`参数：这个参数是给这个`url`取个名字，在项目比较大，`url`比较多的时候用处很大。
4. `kwargs`参数：有时候想给视图传递一个额外的参数，就可以通过`kwargs`参数进行传递。这个参数接收一个字典。传到视图函数的时候，会作为一个关键字传递过去。如下`url`规则：
```python
    from django.urls import path
    from . import views
    
    urlpatterns = [
        path('blog/<int:year>/',views.year_archive,{'foo':'bar'}),
    ]
```
以后在访问`blog/1991/`这个`url`的时候，会将`foo=bar`作为关键寄出了参数传递给`year_archive`函数。

### re_path函数

有时候我们在写`url`匹配的时候，想要定使用正则表达式来实现一些复杂的需求，那么这时候我们可以使用`re_path`来实现。`re_path`的参数和`path`参数一模一样，只不过第一个参数也就是`route`参数可以为一个正则表达式。一些使用`re_path`示例代码如下:
```python
    from django.urls import path,re_path
    from . import views
    
    urlpatterns = [
        path('articles/2003',views.special_case_2003),
        re_path(r'articles/(?P<year>[0-9]{4})/',views.year_archive),
        re_path(r'articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/',views.month_archive),
    ]
```
以上例子中我们可以看到，所有的`route`字符串前面都加了一个`r`，表示这个字符串是一个原生字符串。在写正则表达式中是推荐使用原生字符串的，这样可以避免在`python`这一层面进转义。而且，使用正则表达式捕获参数的时候，是用一个圆操作进行包裹，然后这个参数的名称是通过尖操作`<year>`进行包裹，之后才是写正则表达式的语法。

### include函数

在项目变大以后，经学不会把所有的`url`匹配规则都放在项目的`urls.py`文件中，而是每个`APP`都有自己的`urls.py`文件，在这个文件中存储的都是当前这个`APP`的所有`url`匹配规则。然后再统一注册到项目的`urls.py`文件中。`include`函数有多种用法，这里讲一下两种常用的用法。
1. `include(pattern,namespace=None)`：直接把其他`APP`的`urls`包含进来。示例代码如下：
```python
    from django.contrib import admin
    from django.urls import path,include
    
    urlpatterns = [
        path('admin/',admin.site.urls),
        path('book/',include("book.urls")),
    ]
```
当然也可以传递`namespace`参数来指定一个实例命名空间，但是在使用实例命名空间之前，必须指定一个应用命名空间。示例代码如下：
```python
    # 主urls.py
    
    from django.urls import path,include
    
    urlpatterns = [
        path('movie/',include("movie.urls",namespace='movie')),
    ]
```
然后在`movie/urls.py`中指定应用命名空间。示例代码如下：
```python
    from django.urls import path
    from . import views
    
    # 应用命名空间
    app_name = 'movie'
    
    urlpatterns = [
        path('',views.movie,name='index'),
        path('list/',views.movie_list,name='list'),
    ]
```