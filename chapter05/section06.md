# 生成CSV文件

有时候我们做的网站，需要将一些数据，生成有一个 `CSV`文件给浏览器，并且是作为附件的形式下载下来。以下将讲解如何生成 `CSV`文件。

## 生成小的CSV文件

这里将用一个生成小的 `CSV`文件为例，来把生成 `CSV`文件的技术要点讲到位。我们用 `Python`内置的 `csv`模块来处理 `csv`文件，并且使用 HttpResponse 来将 `csv`文件返回回去。示例代码如下：
```python
    import csv
    from django.http import HttpResponse
    
    def csv_view(request):
        response = HttpResponse(content_type='text/csv')
        response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'
        writer = csv.writer(response)
        writer.writerow(['username', 'age', 'height', 'weight'])
        writer.writerow(['zhiliao', '18', '180', '110'])
        
        return response
```
这里再来对每个部分的代码进行解释：
1. 我们在初始化 `HttpResponse`的时候，指定了 `Content-Type` 为 `text/csv` ，这将告诉浏览器，这是一个 `csv`格式的文件而不是一个 `HTML`格式的文件，如果用默认值，默认值就是 html ，那么浏览器将把 `csv`格式的文件按照 `html`格式输出，这肯定不是我们想要的。
2. 第二个我们还在 `response`中添加一个 `Content-Disposition` 头，这个东西是用来告诉浏览器该如何处理这个文件，我们给这个头的值设置为 `attachment;` ，那么浏览器将不会对这个文件进行显示，而是作为附件的形式下载，第二个 `filename="somefilename.csv"` 是用来指定这个 `csv`文件的名字。
3. 我们使用 `csv`模块的 `writer`方法，将相应的数据写入到 `response`中。
4. 写入中文乱码的时候，需要先导入`codecs`模块，`import codecs`,然后在`csv`写入数据之前增加语句`response.write(codecs.BOM_UTF8)`。

## 将`csv`文件定义成模板

我们还可以将 `csv`格式的文件定义成模板，然后使用 `Django`内置的模板系统，并给这个模板传入一个 `Context`对象，这样模板系统就会根据传入的 `Context`对象，生成具体的 `csv`文件。示例代码如下：
```python
    # 模板文件
    {% for row in data %}
        "{{ row.0|addslashes }}", 
        "{{ row.1|addslashes }}", 
        "{{ row.2|addslashes }}", 
        "{{ row.3|addslashes }}", 
        "{{ row.4|addslashes }}"
    {% endfor %}
    
    # 视图函数
    from django.http import HttpResponse
    from django.template import loader
    
    def some_view(request):
        response = HttpResponse(content_type='text/csv')
        response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'
        csv_data = (
            ('First row', 'Foo', 'Bar', 'Baz'),
            ('Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"),
        )
        
        t = loader.get_template('my_template_name.txt')
        response.write(t.render({"data": csv_data}))
        return response
```

## 生成大的CSV文件

以上的例子是生成的一个小的 `csv`文件，如果想要生成大型的 `csv`文件，那么以上方式将有可能会发生超时的情况（服务器要生成一个大型csv文件，需要的时间可能会超过浏览器默认的超时时间）。这时候我们可以借助另外一个类，叫做 `StreamingHttpResponse`对象，这个对象是将响应的数据作为一个流返回给客户端，而不是作为一个整体返回。示例代码如下：
```python
    class Echo:
        """
        定义一个可以执行写操作的类，以后调用csv.writer的时候，就会执行这个方法
        """
        def write(self, value):
        return value
    def large_csv(request):
        rows = (["Row {}".format(idx), str(idx)] for idx in range(655360))    
        pseudo_buffer = Echo()
        writer = csv.writer(pseudo_buffer)
        response = StreamingHttpResponse((writer.writerow(row) for row in rows),content_type="text/csv")
        response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'
        return response
```
这里我们构建了一个非常大的数据集 `rows`，并且将其变成一个迭代器。然后因为 `StreamingHttpResponse`的第一个参数只能是一个生成器，因此我们使用圆括号 `(writer.writerow(row) for row in rows)` ，并且因为我们要写的文件是 csv 格式的文件，因此需要调用 `writer.writerow` 将 `row`变成一个 csv 格式的字符串。而调用 `writer.writerow` 又需要一个中间的容器，因此这里我们定义了一个非常简单的类 `Echo`，这个类只实现一个 `write`方法，以后在执行 `csv.writer(pseudo_buffer)` 的时候，就会调用 `Echo.writer` 方法。注意： `StreamingHttpResponse`会启动一个进程来和客户端保持长连接，所以会很消耗资源。所以如果不是特殊要求，尽量少用这种方法。

### 关于StreamingHttpResponse

这个类是专门用来处理流数据的。使得在处理一些大型文件的时候，不会因为服务器处理时间过长而到时连接超时。这个类不是继承自 `HttpResponse`，并且跟 `HttpResponse`对比有以下几点区别：
1. 这个类没有属性 `content`，相反是 `streaming_content`。
2. 这个类的 `streaming_content`必须是一个可以迭代的对象。
3. 这个类没有 `write`方法，如果给这个类的对象写入数据将会报错。
注意： `StreamingHttpResponse`会启动一个进程来和客户端保持长连接，所以会很消耗资源。所以如果不是特殊要求，尽量少用这种方法。
