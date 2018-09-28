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

`django.views.generic.base.TemplateView`，这个类视图是专门用来返回模版的。在这个类中，有两个属性是经常需要用到的，一个是 `template_name`，这个属性是用来存储模版的路径， `TemplateView`会自动的渲染这个变量指向的模版。另外一个是 `get_context_data`，这个方法是用来返回上下文数据的，也就是在给模版传的参数的。示例代码如下：
```python
    from django.views.generic.base import TemplateView
    
    class HomePageView(TemplateView):
        template_name = "home.html"
        
        def get_context_data(self, **kwargs):
            context = super().get_context_data(**kwargs)
            context['username'] = "黄老师"
            return context
```
在 `urls.py` 中的映射代码如下：
```python
    from django.urls import path
    from myapp.views import HomePageView
    
    urlpatterns = [
        path('', HomePageView.as_view(), name='home'),
    ]
```
如果在模版中不需要传递任何参数，那么可以直接只在 `urls.py` 中使用 `TemplateView`来渲染模版。示例代码如下：
```python
    from django.urls import path
    from django.views.generic import TemplateView
    
    urlpatterns = [
        path('about/', TemplateView.as_view(template_name="about.html")),
    ]
```

## ListView

在网站开发中，经常会出现需要列出某个表中的一些数据作为列表展示出来。比如文章列表，图书列表等等。在 `Django`中可以使用 `ListView`来帮我们快速实现这种需求。示例代码如下：
```python
    class ArticleListView(ListView):
        model = Article
        template_name = 'article_list.html'
        paginate_by = 10
        context_object_name = 'articles'
        ordering = 'create_time'
        page_kwarg = 'page'
        
        def get_context_data(self, **kwargs):
            context = super(ArticleListView, self).get_context_data(**kwargs)
            print(context)
            return context
        
        def get_queryset(self):
            return Article.objects.filter(id__lte=89)
```
对以上代码进行解释：
1. 首先 `ArticleListView`是继承自 `ListView`。
2. `model`：重写 `model`类属性，指定这个列表是给哪个模型的。
3. `template_name`：指定这个列表的模板。
4. `paginate_by`：指定这个列表一页中展示多少条数据。
5. `context_object_name`：指定这个列表模型在模板中的参数名称。
6. `ordering`：指定这个列表的排序方式。
7. `page_kwarg`：获取第几页的数据的参数名称。默认是 page 。
8. `get_context_data`：获取上下文的数据。
9. `get_queryset`：如果你提取数据的时候，并不是要把所有数据都返回，那么你可以重写这个方法。将一些不需要展示的数据给过滤掉。

## Paginator和Page类

`Paginator`和 `Page`类都是用来做分页的。他们在 `Django`中的路径为 `django.core.paginator.Paginator` 和 django.core.paginator.Page 。以下对这两个类的常用属性和方法做解释：

### Paginator常用属性和方法

1. `count`：总共有多少条数据。
2. `num_pages`：总共有多少页。
3. `page_range`：页面的区间。比如有三页，那么就 range(1,4) 。

### Page常用属性和方法

1. `has_next`：是否还有下一页。
2. `has_previous`：是否还有上一页。
3. `next_page_number`：下一页的页码。
4. `previous_page_number`：上一页的页码。
5. `number`：当前页。
6. `start_index`：当前这一页的第一条数据的索引值。
7. `end_index`：当前这一页的最后一条数据的索引值。

# 给类视图添加装饰器

在开发中，有时候需要给一些视图添加装饰器。如果用函数视图那么非常简单，只要在函数的上面写上装饰器就可以了。但是如果想要给类添加装饰器，那么可以通过以下两种方式来实现：

## 装饰dispatch方法

```python
    from django.utils.decorators import method_decorator
    
    def login_required(func):
        def wrapper(request,*args,**kwargs):
            if request.GET.get("username"):
                return func(request,*args,**kwargs)
            else:
                return redirect(reverse('index'))
        return wrapper
        
    class IndexView(View):
        def get(self,request,*args,**kwargs):
            return HttpResponse("index")
            
        @method_decorator(login_required)
        def dispatch(self, request, *args, **kwargs):
            super(IndexView, self).dispatch(request,*args,**kwargs)
```

## 直接装饰在整个类上

```python
    from django.utils.decorators import method_decorator
    
    def login_required(func):
        def wrapper(request,*args,**kwargs):
            if request.GET.get("username"):
                return func(request,*args,**kwargs)
            else:
                return redirect(reverse('login'))
        return wrapper
        
    @method_decorator(login_required,name='dispatch')
    class IndexView(View):
        def get(self,request,*args,**kwargs):
            return HttpResponse("index")
            
        def dispatch(self, request, *args, **kwargs):
            super(IndexView, self).dispatch(request,*args,**kwargs)
```

