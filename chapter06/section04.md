## 文件上传

文件上传是网站开发中非常常见的功能。这里详细讲述如何在`Django`中实现文件的上传功能。

### 前端HTML代码实现

1. 在前端中，我们需要填入一个`form`标签，然后在这个`form`标签中指定`enctype="multipart/form-data"`，不然就不能上传文件。
2. 在`form`标签中添加一个`input`标签，然后指定`input`标签的`name`，以及`type="file"`。
以下两步的示例代码如下：
```html
    <form action="" method="post" enctype="multipart/form-data">
        <input type="file" name="myfile" />
    </form>
```

### 后端的代码实现

后端的主要工作是接收文件。然后存储文件，接收文件的方式跟接收`POST`的方式是一样的，只不过是通过`FILES`来实现。示例如下：
```python
    dev save_file(file):
        with open('somefile.txt','wb') as fp:
            for chunk in file.chunks():
                fp.write(chunk)
                
    def index(request):
        if request.method == 'GET':
            form = MyForm()
            return render(request,'index.html',{'form':form})
        else:
            myfile = request.FILES.get('myfile')
            save_file(myfile)
            return HttpResponse('Success')
```
以上代码通过`request.FILES`接收到文件后，再写入到指定的地方。这样就可以完成一个文件的上传功能了。


### 使用模型来处理上传的文件

在定义模型的时候，我们可以给存储文件字段指定为`FileField`，这个`Field`可以传递一个`upload_to`参数，用来指定上传上来的文件保存到哪里。比如我们让他保存到项目的`files`目录下，示例代码如下：
```python
    # models.py
    
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        thumbnail = models.FileField(upload_to='files')
        
    # views.py
    
    def index(request):
        if request.method == 'GET':
            return render(request,'index.html')
        else:
            title = request.POST.get('title')
            content = request.POST.get('content')
            thumbnail = request.FILES.get('thumbnail')
            article = Article(title=title,content=content,thumbnail=thumbnail)
            article.save()
```
调用完`article.save()`方法，就会把文件保存到`files`目录下面，并且会将这个文件的路径存储到数据库中。

### 指定`MEDIA_ROOT`和`MEDIA_URL`

以上我们是使用了`upload_to`来指定上传的文件的目录。我们也可以指定`MEDIA_ROOT`,就不需要在`FileField`中指定`upload_to`，他会自动的将文件上传到`MEDIA_ROOT`的目录下。
```python
    MEDIA_ROOT = os.path.join(BASE_DIR,'media')
    MEDIA_URL = '/media/'
```
然后我们可以在`urls.py`中添加`MEDIA_ROOT`目录下的访问路径。示例代码如下：
```python
    from django.urls import path
    from front import views
    from django.conf.urls.static import static
    from django.conf import settings
    
    urlpatterns = [
        path('',views.index),
    ] + static(settings.MEDIA_URL,document_root=settings.MEDIA_ROOT)
```
如果我们同时指定`MEDIA_ROOT`和`upload_to`，那么会将文件上传到`MEDIA_ROOT`下的`upload_to`目录中。示例代码如下：
```python
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        thumbnail = models.FileField(upload_to="%Y/%m/%d/")
```

### 限制上传的文件扩展名

如果想限制上传的文件的扩展名，那么我们就需要用到表单来进行限制。我们可以使用普通的`Form`表单，也可以使用`ModelForm`，直接从模型中读取字段。示例代码如下：
```python
    # models.py
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        thumbnial = models.FileField(upload_to='%Y/%m/%d/',validators=validators[validators.FileExtensionValidator(['txt','pdf'])])

    # forms.py
    class ArticleForm(forms.ModelForm):
        class Meta:
            model = Article
            fields = "__all__"
```

### 上传图片

上传图片跟上传普通文件是一样的。只不过是上传图片的时候`Django`会判断上传的文件是否是图片的格式（除了判断后缀名，还会判断是否是可用的图片）。如果不是，那么就会验证失败。我们首先先来定义一个包含`ImageField`的模型。示例代码如下：
```python
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        thumbnail = models.ImageField(upload_to="%Y/%m/%d/")
```
因为要验证是否是合格的图片，因此我们还需要用一个表单来进行验证。表单我们直接就使用`ModelForm`就可以了。示例代码如下：
```python
    class MyForm(forms.ModelForm):
        class Meta:
            model = Article
            fields = "__all__"
```
**注意：使用ImageField，必须要先安装Pillow库：pip install pillow**
