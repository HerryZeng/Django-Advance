## Django表单API详解

---

声明：以下的`Form`、表单等术语都指的的广义的Django表单。

`Form`要么是绑定了数据的，要么是未绑定数据的。

如果是绑定的，那么它能够验证数据，并渲染表单及其数据，然后生成HTML表单。如果未绑定，则无法进行验证（因为没有数据可以验证！），但它仍然可以以HTML形式呈现空白表单。

表单类原型：class Form

若要创建一个未绑定的Form实例，只需简单地实例化该类：
```python
f = ContactForm()
```
若要绑定数据到表单，可以将数据以字典的形式传递给Form类的构造函数：
```python
    >>> data = {'subject': 'hello',
    ...         'message': 'Hi there',
    ...         'sender': 'foo@example.com',
    ...         'cc_myself': True}
    >>> f = ContactForm(data)
```
在这个字典中，键为字段的名称，它们对应于Form类中的字段。 值为需要验证的数据。

---

### 一、表单的绑定属性

**Form.is_bound：**
如果你需要区分绑定的表单和未绑定的表单，可以检查下表单的`is_bound`属性值：
```python
>>> f = ContactForm()
>>> f.is_bound
False
>>> f = ContactForm({'subject': 'hello'})
>>> f.is_bound
True
```
注意，传递一个空的字典将创建一个带有空数据的绑定的表单：
```python
>>> f = ContactForm({})
>>> f.is_bound
True
```
如果你有一个绑定的Form实例但是想改下数据，或者你想给一个未绑定的Form表单绑定某些数据，你需要创建另外一个Form实例。因为，Form实例的数据没是自读的，Form实例一旦创建，它的数据将不可变。

---

###  二、 使用表单验证数据

1. Form.clean()
如果你要自定义验证功能，那么你需要重新实现这个clean方法。

2. Form.is_valid()
调用is_valid()方法来执行绑定表单的数据验证工作，并返回一个表示数据是否合法的布尔值。
```python
>>> data = {'subject': 'hello',
...         'message': 'Hi there',
...         'sender': 'foo@example.com',
...         'cc_myself': True}
>>> f = ContactForm(data)
>>> f.is_valid()
True
```
让我们试下非法的数据。下面的情形中，subject为空（默认所有字段都是必需的）且sender是一个不合法的邮件地址：
```python
>>> data = {'subject': '',
...         'message': 'Hi there',
...         'sender': 'invalid email address',
...         'cc_myself': True}
>>> f = ContactForm(data)
>>> f.is_valid()
False
```

3. Form.errors
表单的errors属性保存了错误信息字典：
```python
>>> f.errors
{'sender': ['Enter a valid email address.'], 'subject': ['This field is required.']}
```
在这个字典中，键为字段的名称，值为错误信息的Unicode字符串组成的列表。错误信息保存在列表中是因为字段可能有多个错误信息。

4. Form.errors.as_data()
返回一个字典，它将字段映射到原始的ValidationError实例。
```python
>>> f.errors.as_data()
{'sender': [ValidationError(['Enter a valid email address.'])],
'subject': [ValidationError(['This field is required.'])]}
```

5. Form.errors.as_json(escape_html=False)
返回JSON序列化后的错误信息字典。
```python
>>> f.errors.as_json()
{"sender": [{"message": "Enter a valid email address.", "code": "invalid"}],
"subject": [{"message": "This field is required.", "code": "required"}]}
```

6. Form.add_error(field, error)
向表单特定字段添加错误信息。

field参数为字段的名称。如果值为None，error将作为`Form.non_field_errors()`的一个非字段错误。

7. Form.has_error(field, code=None)
判断某个字段是否具有指定code的错误。当code为None时，如果字段有任何错误它都将返回True。

8. Form.non_field_errors()
返回Form.errors中不是与特定字段相关联的错误。

9. 对于没有绑定数据的表单
验证没有绑定数据的表单是没有意义的，下面的例子展示了这种情况：
```python
>>> f = ContactForm()
>>> f.is_valid()
False
>>> f.errors
{}
```

---

### 三、检查表单数据是否被修改

1. Form.has_changed()
当你需要检查表单的数据是否从初始数据发生改变时，可以使用`has_changed()`方法。
```python
>>> data = {'subject': 'hello',
...         'message': 'Hi there',
...         'sender': 'foo@example.com',
...         'cc_myself': True}
>>> f = ContactForm(data, initial=data)
>>> f.has_changed()
False
```
提交表单后，我们可以重新构建表单并提供初始值，进行比较：
```python
>>> f = ContactForm(request.POST, initial=data)
>>> f.has_changed()
```
如果request.POST与initial中的数据有区别，则返回True，否则返回False。

2. Form.changed_data
返回有变化的字段的列表。
```python
>>> f = ContactForm(request.POST, initial=data)
>>> if f.has_changed():
...     print("The following fields changed: %s" % ", ".join(f.changed_data))
```

---

### 四、访问表单中的字段

通过fileds属性访问表单的字段：
```python
>>> for row in f.fields.values(): print(row)
...
<django.forms.fields.CharField object at 0x7ffaac632510>
<django.forms.fields.URLField object at 0x7ffaac632f90>
<django.forms.fields.CharField object at 0x7ffaac3aa050>
>>> f.fields['name']
<django.forms.fields.CharField object at 0x7ffaac6324d0>
```
可以修改Form实例的字段来改变字段在表单中的表示：
```python
>>> f.as_table().split('\n')[0]
'<tr><th>Name:</th><td><input name="name" type="text" value="instance" required /></td></tr>'
>>> f.fields['name'].label = "Username"
>>> f.as_table().split('\n')[0]
'<tr><th>Username:</th><td><input name="name" type="text" value="instance" required /></td></tr>'
```
注意不要改变`base_fields`属性，因为一旦修改将影响同一个Python进程中接下来所有的ContactForm实例：
```python
>>> f.base_fields['name'].label = "Username"
>>> another_f = CommentForm(auto_id=False)
>>> another_f.as_table().split('\n')[0]
'<tr><th>Username:</th><td><input name="name" type="text" value="class" required /></td></tr>'
```

---

### 五、访问cleaned_data

Form.cleaned_data

Form类中的每个字段不仅负责验证数据，还负责将它们转换为正确的格式。例如，`DateField`将输入转换为Python的`datetime.date`对象。无论你传递的是普通字符串'1994-07-15'、`DateField`格式的字符串、`datetime.date`对象、还是其它格式的数字，Django将始终把它们转换成`datetime.date`对象。

一旦你创建一个Form实例并通过验证后，你就可以通过它的cleaned_data属性访问干净的数据：
```python
>>> data = {'subject': 'hello',
...         'message': 'Hi there',
...         'sender': 'foo@example.com',
...         'cc_myself': True}
>>> f = ContactForm(data)
>>> f.is_valid()
True
>>> f.cleaned_data
{'cc_myself': True, 'message': 'Hi there', 'sender': 'foo@example.com', 'subject': 'hello'}
```
如果你的数据没有通过验证，cleaned_data字典中只包含合法的字段：
```python
>>> data = {'subject': '',
...         'message': 'Hi there',
...         'sender': 'invalid email address',
...         'cc_myself': True}
>>> f = ContactForm(data)
>>> f.is_valid()
False
>>> f.cleaned_data
{'cc_myself': True, 'message': 'Hi there'}
```
`cleaned_data`字典始终只包含Form中定义的字段，即使你在构建Form时传递了额外的数据。 在下面的例子中，我们传递了一组额外的字段给ContactForm构造函数，但是`cleaned_data`将只包含表单的字段：
```python
>>> data = {'subject': 'hello',
...         'message': 'Hi there',
...         'sender': 'foo@example.com',
...         'cc_myself': True,
...         'extra_field_1': 'foo',
...         'extra_field_2': 'bar',
...         'extra_field_3': 'baz'}
>>> f = ContactForm(data)
>>> f.is_valid()
True
>>> f.cleaned_data # Doesn't contain extra_field_1, etc.
{'cc_myself': True, 'message': 'Hi there', 'sender': 'foo@example.com', 'subject': 'hello'}
```
当Form通过验证后，`cleaned_data`将包含所有字段的键和值，即使传递的数据中没有提供某些字段的值。 在下面的例子中，提供的实际数据中不包含`nick_name`字段，但是`cleaned_data`任然包含它，只是值为空：
```python
>>> from django import forms
>>> class OptionalPersonForm(forms.Form):
...     first_name = forms.CharField()
...     last_name = forms.CharField()
...     nick_name = forms.CharField(required=False)
>>> data = {'first_name': 'John', 'last_name': 'Lennon'}
>>> f = OptionalPersonForm(data)
>>> f.is_valid()
True
>>> f.cleaned_data
{'nick_name': '', 'first_name': 'John', 'last_name': 'Lennon'}
```

---

### 六、表单的HTML生成方式

Form的第二个任务是将它渲染成HTML代码，默认情况下，根据form类中字段的编写顺序，在HTML中以同样的顺序罗列。 我们可以通过print方法展示出来：
```python
>>> f = ContactForm()
>>> print(f)
<tr><th><label for="id_subject">Subject:</label></th><td><input id="id_subject" type="text" name="subject" maxlength="100" required /></td></tr>
<tr><th><label for="id_message">Message:</label></th><td><input type="text" name="message" id="id_message" required /></td></tr>
<tr><th><label for="id_sender">Sender:</label></th><td><input type="email" name="sender" id="id_sender" required /></td></tr>
<tr><th><label for="id_cc_myself">Cc myself:</label></th><td><input type="checkbox" name="cc_myself" id="id_cc_myself" /></td></tr>
```
如果表单是绑定的，输出的HTML将包含数据。
```python
>>> data = {'subject': 'hello',
...         'message': 'Hi there',
...         'sender': 'foo@example.com',
...         'cc_myself': True}
>>> f = ContactForm(data)
>>> print(f)
<tr><th><label for="id_subject">Subject:</label></th><td><input id="id_subject" type="text" name="subject" maxlength="100" value="hello" required /></td></tr>
<tr><th><label for="id_message">Message:</label></th><td><input type="text" name="message" id="id_message" value="Hi there" required /></td></tr>
<tr><th><label for="id_sender">Sender:</label></th><td><input type="email" name="sender" id="id_sender" value="foo@example.com" required /></td></tr>
<tr><th><label for="id_cc_myself">Cc myself:</label></th><td><input type="checkbox" name="cc_myself" id="id_cc_myself" checked /></td></tr>
```
注意事项：
+ 为了灵活性，输出不包含`<table>`和`</table>`、`<form>`和`</form`>以及`<input type="submit">`标签。 需要我们程序员手动添加它们。
+ 每个字段类型都由一个默认的HTML标签展示。注意，这些只是默认的，可以使用`Widget`特别指定。
+ 每个HTML标签的name属性名直接从ContactForm类中获取。
+ form使用HTML5语法，顶部需添加`<!DOCTYPE html>`说明。
    
1 . 渲染成文字段落`as_p()`
`Form.as_p()`

该方法将form渲染成一系列`<p`>标签，每个`<p>`标签包含一个字段；
```python
>>> f = ContactForm()
>>> f.as_p()
'<p><label for="id_subject">Subject:</label> <input id="id_subject" type="text" name="subject" maxlength="100" required /></p>\n<p><label for="id_message">Message:</label> <input type="text" name="message" id="id_message" required /></p>\n<p><label for="id_sender">Sender:</label> <input type="text" name="sender" id="id_sender" required /></p>\n<p><label for="id_cc_myself">Cc myself:</label> <input type="checkbox" name="cc_myself" id="id_cc_myself" /></p>'
>>> print(f.as_p())
<p><label for="id_subject">Subject:</label> <input id="id_subject" type="text" name="subject" maxlength="100" required /></p>
<p><label for="id_message">Message:</label> <input type="text" name="message" id="id_message" required /></p>
<p><label for="id_sender">Sender:</label> <input type="email" name="sender" id="id_sender" required /></p>
<p><label for="id_cc_myself">Cc myself:</label> <input type="checkbox" name="cc_myself" id="id_cc_myself" /></p>
```

2 . 渲染成无序列表`as_ul()`
`Form.as_ul()`

该方法将form渲染成一系列`<li>`标签，每个`<li>`标签包含一个字段。但不会自动生成`</ul>`和`<ul>`，所以你可以自己指定`<ul>`的任何HTML属性：
```python
>>> f = ContactForm()
>>> f.as_ul()
'<li><label for="id_subject">Subject:</label> <input id="id_subject" type="text" name="subject" maxlength="100" required /></li>\n<li><label for="id_message">Message:</label> <input type="text" name="message" id="id_message" required /></li>\n<li><label for="id_sender">Sender:</label> <input type="email" name="sender" id="id_sender" required /></li>\n<li><label for="id_cc_myself">Cc myself:</label> <input type="checkbox" name="cc_myself" id="id_cc_myself" /></li>'
>>> print(f.as_ul())
<li><label for="id_subject">Subject:</label> <input id="id_subject" type="text" name="subject" maxlength="100" required /></li>
<li><label for="id_message">Message:</label> <input type="text" name="message" id="id_message" required /></li>
<li><label for="id_sender">Sender:</label> <input type="email" name="sender" id="id_sender" required /></li>
<li><label for="id_cc_myself">Cc myself:</label> <input type="checkbox" name="cc_myself" id="id_cc_myself" /></li>
```

3 . 渲染成表格`as_table()`
`Form.as_table()`

渲染成HTML表格。它与print完全相同，事实上，当你print一个表单对象时，在后台调用的就是`as_table()`方法：
```python
>>> f = ContactForm()
>>> f.as_table()
'<tr><th><label for="id_subject">Subject:</label></th><td><input id="id_subject" type="text" name="subject" maxlength="100" required /></td></tr>\n<tr><th><label for="id_message">Message:</label></th><td><input type="text" name="message" id="id_message" required /></td></tr>\n<tr><th><label for="id_sender">Sender:</label></th><td><input type="email" name="sender" id="id_sender" required /></td></tr>\n<tr><th><label for="id_cc_myself">Cc myself:</label></th><td><input type="checkbox" name="cc_myself" id="id_cc_myself" /></td></tr>'
>>> print(f)
<tr><th><label for="id_subject">Subject:</label></th><td><input id="id_subject" type="text" name="subject" maxlength="100" required /></td></tr>
<tr><th><label for="id_message">Message:</label></th><td><input type="text" name="message" id="id_message" required /></td></tr>
<tr><th><label for="id_sender">Sender:</label></th><td><input type="email" name="sender" id="id_sender" required /></td></tr>
<tr><th><label for="id_cc_myself">Cc myself:</label></th><td><input type="checkbox" name="cc_myself" id="id_cc_myself" /></td></tr>
```

---

### 七、 为错误信息添加CSS样式

Form.error_css_class

Form.required_css_class

为一些特别强调的或者需要额外显示的内容设置醒目的CSS样式是一种常用做法，也是非常有必要的。比如给必填字段加粗显示，设置错误文字为红色等等。
`Form.error_css_class`和`Form.required_css_class`属性就是做这个用的：
```python
from django import forms

class ContactForm(forms.Form):
    error_css_class = 'error'
    required_css_class = 'required'

    # ... and the rest of your fields here
```
属性名是固定的，不可变（废话），通过赋值不同的字符串，表示给这两类属性添加不同的CSS的class属性。以后Django在渲染form成HTML时将自动为error和required行添加对应的CSS样式。

上面的例子，其HTML看上去将类似：
```python
>>> f = ContactForm(data)
>>> print(f.as_table())
<tr class="required"><th><label class="required" for="id_subject">Subject:</label>    ...
<tr class="required"><th><label class="required" for="id_message">Message:</label>    ...
<tr class="required error"><th><label class="required" for="id_sender">Sender:</label>      ...
<tr><th><label for="id_cc_myself">Cc myself:<label> ...
>>> f['subject'].label_tag()
<label class="required" for="id_subject">Subject:</label>
>>> f['subject'].label_tag(attrs={'class': 'foo'})
<label for="id_subject" class="foo required">Subject:</label>
```

---

### 八、将上传的文件绑定到表单

处理带有FileField和ImageField字段的表单比普通的表单要稍微复杂一点。
首先，为了上传文件，你需要确保你的`<form>`元素定义`enctype`为`"multipart/form-data"`：
```html
<form enctype="multipart/form-data" method="post" action="/foo/">
```
其次，当你使用表单时，你需要绑定文件数据。文件数据的处理与普通的表单数据是分开的，所以如果表单包含FileField和ImageField，绑定表单时你需要指定第二个参数，参考下面的例子。
```python
# 为表单绑定image字段
>>> from django.core.files.uploadedfile import SimpleUploadedFile
>>> data = {'subject': 'hello',
...         'message': 'Hi there',
...         'sender': 'foo@example.com',
...         'cc_myself': True}
>>> file_data = {'mugshot': SimpleUploadedFile('face.jpg', <file data>)}
>>> f = ContactFormWithMugshot(data, file_data)
```
实际上，一般使用request.FILES作为文件数据的源：
```python
# Bound form with an image field, data from the request
>>> f = ContactFormWithMugshot(request.POST, request.FILES)
```
构造一个未绑定的表单和往常一样，将表单数据和文件数据同时省略：
```python
# Unbound form with an image field
>>> f = ContactFormWithMugshot()
```