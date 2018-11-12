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

1 . **required**

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

2 . **label**

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

3 . **label_suffix**

Django默认为上面的label参数后面加个冒号后缀，如果想自定义，可以使用label_suffix参数。比如下面的例子用“？”代替了冒号：
```python
>>> class ContactForm(forms.Form):
...     age = forms.IntegerField()
...     nationality = forms.CharField()
...     captcha_answer = forms.IntegerField(label='2 + 2', label_suffix=' =')
>>> f = ContactForm(label_suffix='?')
>>> print(f.as_p())
<p><label for="id_age">Age?</label> <input id="id_age" name="age" type="number" required /></p>
<p><label for="id_nationality">Nationality?</label> <input id="id_nationality" name="nationality" type="text" required /></p>
<p><label for="id_captcha_answer">2 + 2 =</label> <input id="id_captcha_answer" name="captcha_answer" type="number" required /></p>
```

4 . **initial**

为HTML页面中表单元素定义初始值。也就是input元素的value参数的值，如下所示：
```python
>>> from django import forms
>>> class CommentForm(forms.Form):
...     name = forms.CharField(initial='Your name')
...     url = forms.URLField(initial='http://')
...     comment = forms.CharField()
>>> f = CommentForm(auto_id=False)
>>> print(f)
<tr><th>Name:</th><td><input type="text" name="name" value="Your name" required /></td></tr>
<tr><th>Url:</th><td><input type="url" name="url" value="http://" required /></td></tr>
<tr><th>Comment:</th><td><input type="text" name="comment" required /></td></tr>
```
你可能会问为什么不在渲染表单的时候传递一个包含初始化值的字典给它，不是更方便？因为如果这么做，你将触发表单的验证过程，此时输出的HTML页面将包含验证中产生的错误，如下所示：
```python
>>> class CommentForm(forms.Form):
...     name = forms.CharField()
...     url = forms.URLField()
...     comment = forms.CharField()
>>> default_data = {'name': 'Your name', 'url': 'http://'}
>>> f = CommentForm(default_data, auto_id=False)
>>> print(f)
<tr><th>Name:</th><td><input type="text" name="name" value="Your name" required /></td></tr>
<tr><th>Url:</th><td><ul class="errorlist"><li>Enter a valid URL.</li></ul><input type="url" name="url" value="http://" required /></td></tr>
<tr><th>Comment:</th><td><ul class="errorlist"><li>This field is required.</li></ul><input type="text" name="comment" required /></td></tr>
```
这就是为什么initial参数只用在未绑定的表单上。
还要注意，如果提交表单时某个字段的值没有填写，initial的值不会作为“默认”的数据。initial值只用于原始表单的显示：
```python
>>> class CommentForm(forms.Form):
...     name = forms.CharField(initial='Your name')
...     url = forms.URLField(initial='http://')
...     comment = forms.CharField()
>>> data = {'name': '', 'url': '', 'comment': 'Foo'}
>>> f = CommentForm(data)
>>> f.is_valid()
False
# The form does *not* fall back to using the initial values.
>>> f.errors
{'url': ['This field is required.'], 'name': ['This field is required.']}
```
除了常量之外，你还可以传递一个可调用的对象：
```python
>>> import datetime
>>> class DateForm(forms.Form):
...     day = forms.DateField(initial=datetime.date.today)
>>> print(DateForm())
<tr><th>Day:</th><td><input type="text" name="day" value="12/23/2008" required /><td></tr>
```

5 . **widget**

最重要的参数之一，指定渲染Widget时使用的widget类，也就是这个form字段在HTML页面中是显示为文本输入框？密码输入框？单选按钮？多选框？还是别的....

6 . **help_text**

该参数用于设置字段的辅助描述文本。
```python
>>> from django import forms
>>> class HelpTextContactForm(forms.Form):
...     subject = forms.CharField(max_length=100, help_text='100 characters max.')
...     message = forms.CharField()
...     sender = forms.EmailField(help_text='A valid email address, please.')
...     cc_myself = forms.BooleanField(required=False)
>>> f = HelpTextContactForm(auto_id=False)
>>> print(f.as_table())
<tr><th>Subject:</th><td><input type="text" name="subject" maxlength="100" required /><br /><span class="helptext">100 characters max.</span></td></tr>
<tr><th>Message:</th><td><input type="text" name="message" required /></td></tr>
<tr><th>Sender:</th><td><input type="email" name="sender" required /><br />A valid email address, please.</td></tr>
<tr><th>Cc myself:</th><td><input type="checkbox" name="cc_myself" /></td></tr>
>>> print(f.as_ul()))
<li>Subject: <input type="text" name="subject" maxlength="100" required /> <span class="helptext">100 characters max.</span></li>
<li>Message: <input type="text" name="message" required /></li>
<li>Sender: <input type="email" name="sender" required /> A valid email address, please.</li>
<li>Cc myself: <input type="checkbox" name="cc_myself" /></li>
>>> print(f.as_p())
<p>Subject: <input type="text" name="subject" maxlength="100" required /> <span class="helptext">100 characters max.</span></p>
<p>Message: <input type="text" name="message" required /></p>
<p>Sender: <input type="email" name="sender" required /> A valid email address, please.</p>
<p>Cc myself: <input type="checkbox" name="cc_myself" /></p>
```

7 . **error_messages**

该参数允许你覆盖字段引发异常时的默认信息。 传递的是一个字典，其键为你想覆盖的错误信息。 例如，下面是默认的错误信息：
```python
>>> from django import forms
>>> generic = forms.CharField()
>>> generic.clean('')
Traceback (most recent call last):
  ...
ValidationError: ['This field is required.']
```
而下面是自定义的错误信息：
```python
>>> name = forms.CharField(error_messages={'required': 'Please enter your name'})
>>> name.clean('')
Traceback (most recent call last):
  ...
ValidationError: ['Please enter your name']
```
可以指定多种类型的键，不仅仅针对‘requeired’错误，参考下面的内容。

8 . **validators**

指定一个列表，其中包含了为字段进行验证的函数。也就是说，如果你自定义了验证方法，不用Django内置的验证功能，那么要通过这个参数，将字段和自定义的验证方法链接起来。

9 . **localize**

localize参数帮助实现表单数据输入的本地化。

10 . **disabled**

设置有该属性的字段在前端页面中将显示为不可编辑状态。
该参数接收布尔值，当设置为True时，使用HTML的disabled属性禁用表单域，以使用户无法编辑该字段。即使非法篡改了前端页面的属性，向服务器提交了该字段的值，也将依然被忽略。

---

### Django表单内置的Field类

对于每个字段类，介绍其默认的widget，当输入为空时返回的值，以及采取何种验证方式。‘规范化为’表示转换为PYthon的何种对象。可用的错误信息键，表示该字段可自定义错误信息的类型（字典的键）。

1 . **BooleanField**

  + 默认的Widget：CheckboxInput
  + 空值：False
  + 规范化为：Python的True或者False
  + 可用的错误信息键：required
  
2 . **CharField**

  + 默认的Widget：TextInput
  + 空值：与empty_value给出的任何值。
  + 规范化为：一个Unicode 对象。
  + 验证max_length或min_length，如果设置了这两个参数。 否则，所有的输入都是合法的。
  + 可用的错误信息键：min_length, max_length, required
有四个可选参数：
  + max_length，min_length：设置字符串的最大和最小长度。
  + strip：如果True（默认），去除输入的前导和尾随空格。
  + empty_value：用来表示“空”的值。 默认为空字符串。
  
3 . **ChoiceField**

  + 默认的Widget：Select
  + 空值：''（一个空字符串）
  + 规范化为：一个Unicode 对象。
  + 验证给定的值是否在选项列表中。
  + 可用的错误信息键：required, invalid_choice
参数choices：用来作为该字段选项的一个二元组组成的可迭代对象（例如，列表或元组）或者一个可调用对象。格式与用于和ORM模型字段的choices参数相同。

4 . **TypedChoiceField**

像ChoiceField一样，只是还有两个额外的参数：coerce和empty_value。
  + 默认的Widget：Select
  + 空值：empty_value参数设置的值。
  + 规范化为：coerce参数类型的值。
  + 验证给定的值在选项列表中存在并且可以被强制转换。
  + 可用的错误信息的键：required, invalid_choice
  
5 . **DateField**

  + 默认的Widget：DateInput
  + 空值：None
  + 规范化为：datetime.date对象。
  + 验证给出的值是一个datetime.date、datetime.datetime 或指定日期格式的字符串。
  + 错误信息的键：required, invalid
  +接收一个可选的参数：input_formats。一个格式的列表，用于转换字符串为datetime.date对象。
如果没有提供input_formats，默认的输入格式为：
```python
['%Y-%m-%d',      # '2006-10-25'
 '%m/%d/%Y',      # '10/25/2006'
 '%m/%d/%y']      # '10/25/06'
```
另外，如果你在设置中指定`USE_L10N=False`，以下的格式也将包含在默认的输入格式中：
```python
['%b %d %Y',      # 'Oct 25 2006'
 '%b %d, %Y',     # 'Oct 25, 2006'
 '%d %b %Y',      # '25 Oct 2006'
 '%d %b, %Y',     # '25 Oct, 2006'
 '%B %d %Y',      # 'October 25 2006'
 '%B %d, %Y',     # 'October 25, 2006'
 '%d %B %Y',      # '25 October 2006'
 '%d %B, %Y']     # '25 October, 2006'
```

6 . **DateTimeField**

  + 默认的Widget：DateTimeInput
  + 空值：None
  + 规范化为：Python的datetime.datetime对象。
  + 验证给出的值是一个datetime.datetime、datetime.date或指定日期格式的字符串。
  + 错误信息的键：required, invalid
接收一个可选的参数：input_formats
如果没有提供input_formats，默认的输入格式为：
```python
['%Y-%m-%d %H:%M:%S',    # '2006-10-25 14:30:59'
 '%Y-%m-%d %H:%M',       # '2006-10-25 14:30'
 '%Y-%m-%d',             # '2006-10-25'
 '%m/%d/%Y %H:%M:%S',    # '10/25/2006 14:30:59'
 '%m/%d/%Y %H:%M',       # '10/25/2006 14:30'
 '%m/%d/%Y',             # '10/25/2006'
 '%m/%d/%y %H:%M:%S',    # '10/25/06 14:30:59'
 '%m/%d/%y %H:%M',       # '10/25/06 14:30'
 '%m/%d/%y']             # '10/25/06'
```  

7 . **DecimalField**

  + 默认的Widget：当Field.localize是False时为NumberInput，否则为TextInput。
  + 空值：None
  + 规范化为：Python decimal对象。
  + 验证给定的值为一个十进制数。 忽略前导和尾随的空白。
  + 错误信息的键：max_whole_digits, max_digits, max_decimal_places,max_value, invalid, required,min_value
接收四个可选的参数：
  + max_value,min_value:允许的值的范围，需要赋值decimal.Decimal对象，不能直接给个整数类型。
  + max_digits：值允许的最大位数（小数点之前和之后的数字总共的位数，前导的零将被删除）。
  + decimal_places：允许的最大小数位。
  
8 . **DurationField**

  + 默认的Widget：TextInput
  + 空值：None
  + 规范化为：Python timedelta。
  + 验证给出的值是一个字符串，而且可以转换为timedelta对象。
  + 错误信息的键：required, invalid.
  
9 . **EmailField**

  + 默认的Widget：EmailInput
  + 空值：''（一个空字符串）
  + 规范化为：Unicode 对象。
  + 使用正则表达式验证给出的值是一个合法的邮件地址。
  + 错误信息的键：required, invalid
两个可选的参数用于验证，max_length 和min_length。  

10 . **FileField**

  + 默认的Widget：ClearableFileInput
  + 空值：None
  + 规范化为：一个UploadedFile对象，它封装文件内容和文件名到一个对象内。
  + 验证非空的文件数据已经绑定到表单。
  + 错误信息的键：missing, invalid, required, empty, max_length
具有两个可选的参数用于验证：max_length 和 allow_empty_file。

11 . **FilePathField**

  + 默认的Widget：Select
  + 空值：None
  + 规范化为：Unicode 对象。
  + 验证选择的选项在选项列表中存在。
  + 错误信息的键：required, invalid_choice
这个字段允许从一个特定的目录选择文件。 它有五个额外的参数，其中的`path`是必须的：
  + path：要列出的目录的绝对路径。 这个目录必须存在。
  + recursive：如果为False（默认值），只用直接位于path下的文件或目录作为选项。如果为True，将递归访问这个目录，其内所有的子目录和文件都将作为选项。
  + match：正则表达模式；只有具有与此表达式匹配的文件名称才被允许作为选项。
  + `allow_files`：可选。默认为True。表示是否应该包含指定位置的文件。它和`allow_folders`必须有一个为True。
  + `allow_folders`可选。默认为False。表示是否应该包含指定位置的目录。
  
12 . **FloatField**

  + 默认的Widget：当Field.localize是False时为NumberInput，否则为TextInput。
  + 空值：None
  + 规范化为：Float 对象。
  + 验证给定的值是一个浮点数。
  + 错误信息的键：max_value, invalid, required, min_value
接收两个可选的参数用于验证，max_value和min_value，控制允许的值的范围。

13 . **ImageField**

  + 默认的Widget：ClearableFileInput
  + 空值：None
  + 规范化为：一个UploadedFile对象，它封装文件内容和文件名为一个单独的对象。
  + 验证文件数据已绑定到表单，并且该文件是Pillow可以解析的图像格式。
  + 错误信息的键：missing, invalid, required, empty, invalid_image
使用ImageField需要安装Pillow（pip install pillow）。如果在上传图片时遇到图像损坏错误，通常意味着使用了Pillow不支持的格式。

14 . **IntegerField**

  + 默认的Widget：当Field.localize是False时为NumberInput，否则为TextInput。
  + 空值：None
  + 规范化为：Python 整数或长整数。
  + 验证给定值是一个整数。 允许前导和尾随空格，类似Python的int()函数。
  + 错误信息的键：max_value, invalid, required, min_value
两个可选参数：max_value和min_value，控制允许的值的范围。

15 . **GenericIPAddressField**

包含IPv4或IPv6地址的字段。
  + 默认的Widget：TextInput
  + 空值：''（一个空字符串）
  + 规范化为：一个Unicode对象。
  + 验证给定值是有效的IP地址。
  + 错误信息的键：required, invalid
有两个可选参数：protocol和unpack_ipv4

16 . **MultipleChoiceField**

  + 默认的Widget：SelectMultiple
  + 空值：[]（一个空列表）
  + 规范化为：一个Unicode 对象列表。
  + 验证给定值列表中的每个值都存在于选择列表中。
  + 错误信息的键：invalid_list, invalid_choice, required
  
17 . **TypedMultipleChoiceField**

类似MultipleChoiceField，除了需要两个额外的参数，coerce和empty_value。
  + 默认的Widget：SelectMultiple
  + 空值：empty_value
  + 规范化为：coerce参数提供的类型值列表。
  + 验证给定值存在于选项列表中并且可以强制。
  + 错误信息的键：required, invalid_choice
  
18 . **NullBooleanField**

  + 默认的Widget：NullBooleanSelect
  + 空值：None
  + 规范化为：Python None, False 或True 值。
  + 不验证任何内容（即，它从不引发ValidationError）。
  
19 . **RegexField**

  + 默认的Widget：TextInput
  + 空值：''（一个空字符串）
  + 规范化为：一个Unicode 对象。
  + 验证给定值与某个正则表达式匹配。
  + 错误信息的键：required, invalid
需要一个必需的参数：`regex`，需要匹配的正则表达式。
还可以接收max_length，min_length和strip参数，类似CharField。

20 . **SlugField**

  + 默认的Widget：TextInput
  + 空值：''（一个空字符串）
  + 规范化为：一个Unicode 对象。
  + 验证给定的字符串只包括字母、数字、下划线及连字符。
  + 错误信息的键：required, invalid
此字段用于在表单中表示模型的SlugField。

21 . **TimeField**

  + 默认的Widget：TextInput
  + 空值：None
  + 规范化为：一个Python 的datetime.time 对象。
  + 验证给定值是datetime.time或以特定时间格式格式化的字符串。
  + 错误信息的键：required, invalid
接收一个可选的参数：input_formats，用于尝试将字符串转换为有效的datetime.time对象的格式列表。

如果没有提供input_formats，默认的输入格式为：
```python
'%H:%M:%S',     # '14:30:59'
'%H:%M',        # '14:30'
```

22 . **URLField**

  + 默认的Widget：URLInput
  + 空值：''（一个空字符串）
  + 规范化为：一个Unicode 对象。
  + 验证给定值是个有效的URL。
  + 错误信息的键：required, invalid
可选参数：max_length和min_length

23 . **UUIDField**

  + 默认的Widget：TextInput
  + 空值：''（一个空字符串）
  + 规范化为：UUID对象。
  + 错误信息的键：required, invalid
  
24 . **ComboField**

  + 默认的Widget：TextInput
  + 空值：''（一个空字符串）
  + 规范化为：Unicode 对象。
  + 根据指定为ComboField的参数的每个字段验证给定值。
  + 错误信息的键：required, invalid
接收一个额外的必选参数：fields，用于验证字段值的字段列表（按提供它们的顺序）。
```python
>>> from django.forms import ComboField
>>> f = ComboField(fields=[CharField(max_length=20), EmailField()])
>>> f.clean('test@example.com')
'test@example.com'
>>> f.clean('longemailaddress@example.com')
Traceback (most recent call last):
...
ValidationError: ['Ensure this value has at most 20 characters (it has 28).']
```

25 . **MultiValueField**

  + 默认的Widget：TextInput
  + 空值：''（一个空字符串）
  + 规范化为：子类的compress方法返回的类型。
  + 根据指定为MultiValueField的参数的每个字段验证给定值。
  + 错误信息的键：incomplete, invalid, required
  
26 . **SplitDateTimeField**

  + 默认的Widget：SplitDateTimeWidget
  + 空值：None
  + 规范化为：Python datetime.datetime 对象。
  + 验证给定的值是datetime.datetime或以特定日期时间格式格式化的字符串。
  + 错误信息的键：invalid_date, invalid, required, invalid_time
  
---

### 三、创建自定义字段

如果内置的`Field`真的不能满足你的需求，还可以自定义`Field`。

只需要创建一个`django.forms.Field`的子类，并实现`clean(`)和`__init__()`构造方法。`__init__()`构造方法需要接收前面提过的那些核心参数，比如`widget、required,、label、help_text、initial`。

还可以通过覆盖`get_bound_field()`方法来自定义访问字段的方式。

