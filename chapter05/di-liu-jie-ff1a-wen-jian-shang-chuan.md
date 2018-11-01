## 文件上传

Django在处理文件上传时，文件数据被打包封装在request.FILES中。

### 一、简单上传

首先，写一个form模型，它必须包含一个FileField：
```python
# forms.py
from django import forms

class UploadFileForm(forms.Form):
    file = forms.FileField()
```
处理这个表单的视图将在`request.FILES`中收到文件数据，可以用`request.FILES['file']`来获取上传文件的具体数据，其中的键值‘`file`’是根据`file = forms.FileField()`的变量名来的。

注意：`request.FILES`只有在请求方法为POST,并且提交请求的`<form>`具有`enctype="multipart/form-data"`属性时才有效。 否则，`request.FILES`将为空。

下面是一个接收上传文件的视图范例：
```python
# views.py

from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import UploadFileForm

# 另外写一个处理上传过来的文件的方法，并在这里导入
from somewhere import handle_uploaded_file

def upload_file(request):
    if request.method == 'POST':
        form = UploadFileForm(request.POST, request.FILES) # 注意获取数据的方式
        if form.is_valid():
            handle_uploaded_file(request.FILES['file'])
            return HttpResponseRedirect('/success/url/')
    else:
        form = UploadFileForm()
    return render(request, 'upload.html', {'form': form})
```
请注意，必须将request.FILES传递到form的构造函数中。
```python
form = UploadFileForm(request.POST, request.FILES)
```
下面是一个处理上传文件的方法的参考例子：
```python
def handle_uploaded_file(f):
    with open('some/file/name.txt', 'wb+') as destination:
        for chunk in f.chunks():
            destination.write(chunk)
```
遍历`UploadedFile.chunks()`，而不是直接使用`read()`方法，能确保大文件不会占用系统过多的内存

---

### 二、使用模型处理上传的文件

如果是通过模型层的model来指定上传文件的保存方式的话，使用ModelForm更方便。 调用form.save()的时候，文件对象会保存在相应的FileField的upload_to参数指定的地方。
```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .models import ModelFormWithFileField

def upload_file(request):
    if request.method == 'POST':
        form = ModelFormWithFileField(request.POST, request.FILES)
        if form.is_valid():
            # 这么做就可以了，文件会被保存到Model中upload_to参数指定的位置
            form.save()
            return HttpResponseRedirect('/success/url/')
    else:
        form = ModelFormWithFileField()
    return render(request, 'upload.html', {'form': form})
```
如果手动构造一个对象，还可以简单地把文件对象直接从request.FILES赋值给模型：
```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import UploadFileForm
from .models import ModelWithFileField

def upload_file(request):
    if request.method == 'POST':
        form = UploadFileForm(request.POST, request.FILES)
        if form.is_valid():
            instance = ModelWithFileField(file_field=request.FILES['file'])
            instance.save()
            return HttpResponseRedirect('/success/url/')
    else:
        form = UploadFileForm()
    return render(request, 'upload.html', {'form': form})
```