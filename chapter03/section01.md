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