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