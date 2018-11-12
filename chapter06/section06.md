## Django表单字段汇总

`Field.clean(value)
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

### 