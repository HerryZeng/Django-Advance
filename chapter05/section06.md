# 类视图

在写视图的时候， `Django`除了使用函数作为视图，也可以使用类作为视图。使用类视图可以使用类的一些特性，比如继承等。

## View

`django.views.generic.base.View`是主要的类视图，所有的类视图都是继承自它。如果我们写自己的类视图，也可以继承自它。然后再根据当前请求的 `method`，来实现不同的方法。比如这个视图只能使用 `get`的方式来请求，那么就可以在这个类中定义 `get(self,request,*args,**kwargs)` 方法。以此类推，如果只需要实现 `post`方法，那么就只需
要在类中实现 `post(self,request,*args,**kwargs)` 。示例代码如下：
```python
    from django.views import View
    
    class BookDetailView(View):
        def get(self,request,*args,**kwargs):
            return render(request,'detail.html')
```
类视图写完后，还应该在 `urls.py` 中进行映射，映射的时候就需要调用 `View`的类方法 `as_view()` 来进行转换。示例代码如下：
```python
    urlpatterns = [
        path("detail/<book_id>/",views.BookDetailView.as_view(),name='detail'),
    ]
```
除了 `get`方法， `View`还支持以下方法 `['get','post','put','patch','delete','head','options','trace']` 。
如果用户访问了 `View`中没有定义的方法。比如你的类视图只支持 get 方法，而出现了 `post`方法，那么就会把这个请求转发给`http_method_not_allowed(request,*args,**kwargs)` 。示例代码如下：
```python
    class AddBookView(View):
    def post(self,request,*args,**kwargs):
        return HttpResponse("书籍添加成功！")
        
    def http_method_not_allowed(self, request, *args, **kwargs):
        return HttpResponse("您当前采用的method是：%s，本视图只支持使用post请求！" % request.method)
```
`urls.py` 中的映射如下:
```python
    path("addbook/",views.AddBookView.as_view(),name='add_book'),
```
如果你在浏览器中访问 `addbook/` ，因为浏览器访问采用的是 `get`方法，而 `addbook`只支持 `post`方法，因此以上视图会返回您当前采用的 `method`是： GET ，本视图只支持使用 `post`请求！。
其实不管是 `get`请求还是 `post`请求，都会走 `dispatch(request,*args,**kwargs)` 方法，所以如果实现这个方法，将能够对所有请求都处理到。

## TemplateView

