# ModelForm

大家在写表单的时候，会发现表单中的 `Field`和模型中的 `Field`基本上是一模一样的，而且表单中需要验证的数据，也就是我们模型中需要保存的。那么这时候我们就可以将模型中的字段和表单中的字段进行绑定。
比如现在有个 Article 的模型。示例代码如下：
```python
    from django.db import models
    from django.core import validators
    
    class Article(models.Model):
        title = models.CharField(max_length=10,validators=[validators.MinLengthValidator(limit_value=3)])
        content = models.TextField()
        author = models.CharField(max_length=100)
        category = models.CharField(max_length=100)
        create_time = models.DateTimeField(auto_now_add=True)
```
那么在写表单的时候，就不需要把 `Article`模型中所有的字段都一个个重复写一遍了。示例代码如下：
```python
    from django import forms
    
    class MyForm(forms.ModelForm):
        class Meta:
            model = Article
            fields = "__all__"
```
`MyForm`是继承自 `forms.ModelForm` ，然后在表单中定义了一个 `Meta`类，在 `Meta`类中指定了 `model=Article` ，以及 `fields="__all__"` ，这样就可以将 `Article `模型中所有的字段都复制过来，进行验证。如果只想针对其中几个字段进行验证，那么可以给 `fields`指定一个列表，将需要的字段写进去。比如只想验证 `title`和 `content`，那么可以使用以下代码实现：
```python
    from django import forms
    
    class MyForm(forms.ModelForm):
        class Meta:
            model = Article
            fields = ['title','content']
```
如果要验证的字段比较多，只是除了少数几个字段不需要验证，那么可以使用 `exclude`来代替 `fields`。比如我不想验证 `category`，那么示例代码如下：
```python
    class MyForm(forms.ModelForm):
        class Meta:
            model = Article
            exclude = ['category']
```

## 自定义错误消息

使用 `ModelForm`，因为字段都不是在表单中定义的，而是在模型中定义的，因此一些错误消息无法在字段中定义。那么这时候可以在 `Meta`类中，定义 `error_messages`，然后把相应的错误消息写到里面去。示例代码如下：
```python
    class MyForm(forms.ModelForm):
    class Meta:
        model = Article
        exclude = ['category']
        error_messages ={
            'title':{
                'max_length': '最多不能超过10个字符！',
                'min_length': '最少不能少于3个字符！'
            },
            'content': {
                'required': '必须输入content！',
            }
        }
```

### save方法

`ModelForm`还有 `save`方法，可以在验证完成后直接调用 `save`方法，就可以将这个数据保存到数据库中了。示例代码如下：
```python
    form = MyForm(request.POST)
    if form.is_valid():
        form.save()
        return HttpResponse('succes')
    else:
        print(form.get_errors())
        return HttpResponse('fail')
```
这个方法必须要在 `clean`没有问题后才能使用，如果在 `clean`之前使用，会抛出异常。另外，我们在调用 `save`方法的时候，如果传入一个 `commit=False` ，那么只会生成这个模型的对象，而不会把这个对象真正的插入到数据库中。比如表单上验证的字段没有包含模型中所有的字段，这时候就可以先创建对象，再根据填充其他字段，把所有字段的值都补充完成后，再保存到数据库中。示例代码如下：
```python
    form = MyForm(request.POST)
    if form.is_valid():
        article = form.save(commit=False)
        article.category = 'Python'
        article.save()
        return HttpResponse('succes')
    else:
        print(form.get_errors())
        return HttpResponse('fail')
```