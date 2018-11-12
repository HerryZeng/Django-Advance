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

### ModelForm的验证

验证ModelForm主要分两步：
    + 验证表单
    + 验证模型实例
与普通的表单验证类似，模型表单的验证也是调用`is_valid()`方法或访问`errors`属性。模型的验证（`Model.full_clean()`）紧跟在表单的`clean()`方法调用之后。通常情况下，我们使用Django内置的验证器就好了。如果需要，可以重写模型表单的`clean()`来提供额外的验证，方法和普通的表单一样。

### ModelForm的字段选择

强烈建议使用`ModelForm`的`fields`属性，在赋值的列表内，一个一个将要使用的字段添加进去。这样做的好处是，安全可靠。

然而，有时候，字段太多，或者我们想偷懒，不愿意一个一个输入，也有简单的方法：

`__all__`:

将fields属性的值设为`__all__`，表示将映射的模型中的全部字段都添加到表单类中来。
```python
from django.forms import ModelForm

class AuthorForm(ModelForm):
    class Meta:
        model = Author
        fields = '__all__'
```

**exclude**属性

表示将model中，除了exclude属性中列出的字段之外的所有字段，添加到表单类中作为表单字段。
```python
class PartialAuthorForm(ModelForm):
    class Meta:
        model = Author
        exclude = ['birth_date']
```
因为`Author`模型有3个字段`name`、`title`和`birth_date`，上面的例子会让`title`和`name`出现在表单中。

### 自定义ModelForm字段

在前面，我们有个表格，展示了从模型到模型表单在字段上的映射关系。通常，这是没有什么问题，直接使用，按默认的来就行了。但是，有时候可能这种默认映射关系不是我们想要的，或者想进行一些更加灵活的定制，那怎么办呢？

**使用Meta类内部的widgets属性！**

`widgets`属性接收一个数据字典。其中每个元素的键必须是模型中的字段名之一，键值就是我们要自定义的内容了，具体格式和写法，参考下面的例子。
例如，如果你想要让Author模型中的name字段的类型从`CharField`更改为`<textarea>`，而不是默认的`<input type="text">`，可以如下重写字段的`Widget`：
```python
from django.forms import ModelForm, Textarea
from myapp.models import Author

class AuthorForm(ModelForm):
    class Meta:
        model = Author
        fields = ('name', 'title', 'birth_date')
        widgets = {
            'name': Textarea(attrs={'cols': 80, 'rows': 20}), # 关键是这一行
        }
```
上面还展示了添加样式参数的格式。

如果你希望进一步自定义字段，还可以指定`Meta`类内部的`error_messages`、`help_texts`和`labels`属性，比如：
```python
from django.utils.translation import ugettext_lazy as _

class AuthorForm(ModelForm):
    class Meta:
        model = Author
        fields = ('name', 'title', 'birth_date')
        labels = {
            'name': _('Writer'),
        }
        help_texts = {
            'name': _('Some useful help text.'),
        }
        error_messages = {
            'name': {
                'max_length': _("This writer's name is too long."),
            },
        }
```
还可以指定`field_classe`s属性将字段类型设置为你自己写的表单字段类型。
例如，如果你想为`slug`字段使用`MySlugFormField`，可以像下面这样：
```python
from django.forms import ModelForm
from myapp.models import Article

class ArticleForm(ModelForm):
    class Meta:
        model = Article
        fields = ['pub_date', 'headline', 'content', 'reporter', 'slug']
        field_classes = {
            'slug': MySlugFormField,
        }
```
最后，如果你想完全控制一个字段,包括它的类型，验证器，是否必填等等。可以显式地声明或指定这些性质，就像在普通表单中一样。比如，如果想要指定某个字段的验证器，可以显式定义字段并设置它的`validators`参数：
```python
from myapp.models import Article

class ArticleForm(ModelForm):
    slug = CharField(validators=[validate_slug])

    class Meta:
        model = Article
        fields = ['pub_date', 'headline', 'content', 'reporter', 'slug']
```

### 启用字段本地化

默认情况下，`ModelForm`中的字段不会本地化它们的数据。可以使用`Meta`类的`localized_fields`属性来启用字段的本地化功能。
```python
>>> from django.forms import ModelForm
>>> from myapp.models import Author
>>> class AuthorForm(ModelForm):
...     class Meta:
...         model = Author
...         localized_fields = ('birth_date',)
```
如果`localized_fields`设置为`__all__`这个特殊的值，所有的字段都将本地化。

### 表单的继承

`ModelForms`是可以被继承的。子模型表单可以添加额外的方法和属性，比如下面的例子：
```python
>>> class EnhancedArticleForm(ArticleForm):
...     def clean_pub_date(self):
...         ...
```
以上创建了一个`ArticleForm`的子类`EnhancedArticleForm`，并增加了一个`clean_pub_date`方法。

还可以修改`Meta.fields`或`Meta.exclude`列表，只要继承父类的`Meta`类，如下所示：
```python
>>> class RestrictedArticleForm(EnhancedArticleForm):
...     class Meta(ArticleForm.Meta):
...         exclude = ('body',)
```

### 提供初始值

可以在实例化一个表单时通过指定initial参数来提供表单中数据的初始值。
```python
>>> article = Article.objects.get(pk=1)
>>> article.headline
'My headline'
>>> form = ArticleForm(initial={'headline': 'Initial headline'}, instance=article)
>>> form['headline'].value()
'Initial headline'
```