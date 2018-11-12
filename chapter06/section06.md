## Django表单字段汇总

`Field.clean(value)`
虽然表单字段的Field类主要使用在Form类中，但也可以直接实例化它们来使用，以便更好地了解它们是如何工作的。每个Field的实例都有一个`clean()`方法，它接受一个参数，然后返回“清洁的”数据或者抛出一个`django.forms.ValidationError`异常：
```python
>>> from django import forms
>>> f = forms.EmailField()
>>> f.clean('foo@example.com')
'foo@example.com'
>>> f.clean('invalid email address')
Traceback (most recent call last):
...
ValidationError: ['Enter a valid email address.']
```
这个clean方法经常被我们用来在开发或测试过程中对数据进行验证和测试。

---

### 一、核心字段参数

以下的参数是每个Field类都可以使用的。

1. **required**
给字段添加必填属性，不能空着。
```python
>>> from django import forms
>>> f = forms.CharField()
>>> f.clean('foo')
'foo'
>>> f.clean('')
Traceback (most recent call last):
...
ValidationError: ['This field is required.']
>>> f.clean(None)
Traceback (most recent call last):
...
ValidationError: ['This field is required.']
>>> f.clean(' ')
' '
>>> f.clean(0)
'0'
>>> f.clean(True)
'True'
>>> f.clean(False)
'False'
```

若要表示一个字段不是必需的，设置`required=False`：
```python
>>> f = forms.CharField(required=False)
>>> f.clean('foo')
'foo'
>>> f.clean('')
''
>>> f.clean(None)
''
>>> f.clean(0)
'0'
>>> f.clean(True)
'True'
>>> f.clean(False)
'False'
```

2. **label**
label参数用来给字段添加‘人类友好’的提示信息。如果没有设置这个参数，那么就用字段的首字母大写名字。比如：

下面的例子，前两个字段有，最后的`comment`没有`labe`l参数：
```python
>>> from django import forms
>>> class CommentForm(forms.Form):
...     name = forms.CharField(label='Your name')
...     url = forms.URLField(label='Your website', required=False)
...     comment = forms.CharField()
>>> f = CommentForm(auto_id=False)
>>> print(f)
<tr><th>Your name:</th><td><input type="text" name="name" required /></td></tr>
<tr><th>Your website:</th><td><input type="url" name="url" /></td></tr>
<tr><th>Comment:</th><td><input type="text" name="comment" required /></td></tr>
```

3. **label_suffix**

