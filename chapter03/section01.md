# 模板

在之前章节中，视图函数只是直接返回文本，而在实际生产环境中其实很少这样用，因为实际的页面大多是带有格式的**HTML**代码，这可以让浏览器被渲染出非常漂亮的页面。目前市面上有非常多的模板系统，其中最知名最好用的就是**DTL**和**Jinja2**。`DTL`是`Django Template Language`三个单词的缩写，也就是Django自带的模板语言。当然也可以配置**Django**支持**Jinja2**等其他模板引擎，但是作为**Django**内置的模板语言，和**Django**可以达到无缝衔接而不会产生一些不兼容的情况。因此建议大家学习好**DTL**。


# DTL与普通的HTML文件的区别

**DTL**模板是一种带有特殊语法的**HTML**文件，这个**HTML**文件可以被**Django**编译，可以传递参数进去，实现数据动态化。在编译完成后，生成一个普通的**HTML**文件，然后发送给客户端。

# 渲染模板

渲染模板有多种方式。这里讲一下两种常用的方式。

1. `render_to_string`:找到模板，然后将模板编译后渲染成**Python**的字符串格式。最后再通过`HttpResponse`类包装成一个`HttpResponse`对象返回回去。示例代码如下：
```Python
    from django.template.loader import render_to_string
    from django.http import HttpResponse
    
    def book_detail(request):
        html = render_to_string("detail.html")
        return HttpResponse(html)
```

2. 以上方式虽然已经很方便了。但是**Django**还提供了一个更加简便的方式，直接将模板渲染成字符串和包装成`HttpResponse`对象一步到位完成。示例代码如下：
```Python
    from django.shortcuts import render
    
    def book_list(request):
        return render(request,'list.html')
```

# 模板查找路径配置

在项目的`settings.py`文件中，有一个`TEMPLATES`配置，这个配置包含了模板引擎的配置，模板查找路径的配置，模板上下文的配置等。模板路径可以在两个地方配置。

1. `DIRS`:这是一个列表，在这个列表中可以存放所有的模板路径，以后在视图中使用`render`或`render_to_string`渲染模板的时候，会在这个列表的路径中查找模板。

2. `APP_DIRS`:默认为`True`，这个设置为`True`,会在`INSTALLED_APPS`的`APP`下的`templates`目录中查找模板。

3. 查找顺序：比如代码`render(request,'list.html')`。会先在`DIRS`这个列表中依次查找路径下有没有这个模板，如果有，就返回。如果`DIRS`列表中所有的路径都没有找到，那么就先检查当前这个视图所处的`APP`是否已安装，如果已安装了，那么就先在当前这个`APP`下的`templates`目录中查找模板，如果没有找到，那么会在其他已安装的`APP`的`templates`目录中查找模板。如果所有路径下都没有找到，那么会抛出一个`TemplageDoesNotExist`的异常。