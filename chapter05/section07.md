## 动态生成PDF文件

---

可以通过开源的Python PDF库`ReportLab`来实现PDF文件的动态生成。

---

### 一、安装ReportLab

ReportLab库在PyPI上提供，可以使用pip来安装：
```bash
$ pip install reportlab
```
在Python交互解释器中导入它来测试安装：
```python
>>> import reportlab
```
如果没有抛出任何错误，证明已安装成功。

---

### 二、编写视图

ReportLab的API可以处理于类似于文件（file-like）的对象。下面是一个 “Hello World”的例子：
```python
from reportlab.pdfgen import canvas
from django.http import HttpResponse

def some_view(request):
    # 创建带有PDF头部定义的HttpResponse对象
    response = HttpResponse(content_type='application/pdf')
    response['Content-Disposition'] = 'attachment; filename="somefilename.pdf"'

    # 创建一个PDF对象，并使用响应对象作为它要处理的‘文件’
    p = canvas.Canvas(response)

    # 通过PDF对象的drawString方法，写入一条信息。具体参考模块的官方文档说明。
    p.drawString(100, 100, "Hello world.")

    # 关闭PDF对象
    p.showPage()
    p.save()
    return response
```
相关说明：
+ 响应对象的MIME类型为`application/pdf`。 这会告诉浏览器，文档是个PDF文件而不是HTML文件。
+ 响应对象设置了附加的`Content-Disposition`协议头，含有PDF文件的名称。
+ 文件名可以是任意的，浏览器会在“另存为...”对话框等中使用。
+ `Content-Disposition`以'attachment'开头，强制让浏览器弹出对话框来提示或者确认。
+ Canvas函数接受一个类似于文件的对象，而HttpResponse对象正好合适。
+ 最后，在PDF文件上调用showPage()和save()方法非常重要。
+ 注意：ReportLab并不是线程安全的。

---


### 三、复杂的PDF

使用ReportLab创建复杂的PDF文档时，可以考虑使用io库作为PDF文件的临时保存地点。这个库提供了一个类似于文件的对象接口，非常实用。 下面的例子是上面的“Hello World”示例采用io重写后的样子：
```python
from io import BytesIO
from reportlab.pdfgen import canvas
from django.http import HttpResponse

def some_view(request):
    # Create the HttpResponse object with the appropriate PDF headers.
    response = HttpResponse(content_type='application/pdf')
    response['Content-Disposition'] = 'attachment; filename="somefilename.pdf"'

    buffer = BytesIO()

    # Create the PDF object, using the BytesIO object as its "file."
    p = canvas.Canvas(buffer)

    # Draw things on the PDF. Here's where the PDF generation happens.
    # See the ReportLab documentation for the full list of functionality.
    p.drawString(100, 100, "Hello world.")

    # Close the PDF object cleanly.
    p.showPage()
    p.save()

    # Get the value of the BytesIO buffer and write it to the response.
    pdf = buffer.getvalue()
    buffer.close()
    response.write(pdf)
    return response
```