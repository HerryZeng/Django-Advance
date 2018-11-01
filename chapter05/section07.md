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