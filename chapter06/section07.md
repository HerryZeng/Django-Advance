## 表单的Widgets

不要将`Widget`与表单的`fields`字段混淆。表单字段负责验证输入并直接在模板中使用。而`Widget`负责渲染网页上HTML表单的输入元素和提取提交的原始数据。`widget`是字段的一个内在属性，用于定义字段在浏览器的页面里以何种HTML元素展现。

---

### 一、指定使用的widget

每个字段都有一个默认的widget类型。如果你想要使用一个不同的Widget，可以在定义字段时使用widget参数。 像这样：
```python
from django import forms

class CommentForm(forms.Form):
    name = forms.CharField()
    url = forms.URLField()
    comment = forms.CharField(widget=forms.Textarea)
```
这将使用一个Textarea Widget来展现表单的评论字段，而不是默认的TextInput Widget。

---

### 二、设置widget的参数

许多`widget`具有可选的额外参数，下面的示例中，设置了`SelectDateWidget`的`years` 属性，注意参数的传递方法：
```python
from django import forms

BIRTH_YEAR_CHOICES = ('1980', '1981', '1982')
FAVORITE_COLORS_CHOICES = (
    ('blue', 'Blue'),
    ('green', 'Green'),
    ('black', 'Black'),
)

class SimpleForm(forms.Form):
    birth_year = forms.DateField(widget=forms.SelectDateWidget(years=BIRTH_YEAR_CHOICES))
    favorite_colors = forms.MultipleChoiceField(
        required=False,
        widget=forms.CheckboxSelectMultiple,
        choices=FAVORITE_COLORS_CHOICES,
    )
```

---

### 三、为widget添加CSS样式

默认情况下，当Django渲染Widget为实际的HTML代码时，不会帮你添加任何的CSS样式，也就是说网页上所有的TextInput元素的外观是一样的。

看下面的表单：
```python
from django import forms

class CommentForm(forms.Form):
    name = forms.CharField()
    url = forms.URLField()
    comment = forms.CharField()
```
这个表单包含三个默认的TextInput Widget，以默认的方式渲染，没有CSS类、没有额外的属性。每个Widget的输入框将渲染得一模一样，丑陋又单调：
```python
>>> f = CommentForm(auto_id=False)
>>> f.as_table()
<tr><th>Name:</th><td><input type="text" name="name" required /></td></tr>
<tr><th>Url:</th><td><input type="url" name="url" required /></td></tr>
<tr><th>Comment:</th><td><input type="text" name="comment" required /></td></tr>
```
在真正的网页中，你不想让每个Widget看上去都一样。可能想要给comment一个更大的输入框，可能想让`‘name’ Widget`具有一些特殊的CSS类。

可以在创建`Widge`t时使用`Widget.attrs`参数来实现这一目的：
```python
class CommentForm(forms.Form):
    name = forms.CharField(widget=forms.TextInput(attrs={'class': 'special'}))
    url = forms.URLField()
    comment = forms.CharField(widget=forms.TextInput(attrs={'size': '40'}))
```
**注意参数的传递方式！**

这次渲染后的结果就不一样了：
```python
>>> f = CommentForm(auto_id=False)
>>> f.as_table()
<tr><th>Name:</th><td><input type="text" name="name" class="special" required /></td></tr>
<tr><th>Url:</th><td><input type="url" name="url" required /></td></tr>
<tr><th>Comment:</th><td><input type="text" name="comment" size="40" required /></td></tr>
```
