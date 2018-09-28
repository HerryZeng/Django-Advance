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
2. 第二个我们还在 `response`中添加一个 `Content-Disposition` 头，这个东西是用来告诉浏览器该如何处理这个文件，我们给这个头的值设置为 `attachment`; ，那么浏览器将不会对这个文件进行显示，而是作为附件的形式下载，第二个 `filename="somefilename.csv"` 是用来指定这个 `csv`文件的名字。
3. 我们使用 `csv`模块的 `writer`方法，将相应的数据写入到 `response`中。

## 将`csv`文件定义成模板

