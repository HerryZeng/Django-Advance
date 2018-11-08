# 表单

## HTML中的表单

单纯从前端的 `html`来说，表单是用来提交数据给服务器的,不管后台的服务器用的是 `Django`还是 `PHP`语言还是其他语言。只要把 `input`标签放在 `form`标签中，然后再添加一个提交按钮，那么以后点击提交按钮，就可以将 `input`标签中对应的值提交给服务器了。

## Django中的表单

`Django`中的表单丰富了传统的 `HTML`语言中的表单。在 `Django`中的表单，主要做以下两件事：
1. 渲染表单模板。
2. 表单验证数据是否合法。

## Django中表单使用流程

1. 编写表单类
在讲解 `Django`表单的具体每部分的细节之前。我们首先先来看下整体的使用流程。这里以一个做一个留言板为例。首先我们在后台服务器定义一个表单类，继承自 django.forms.Form 。示例代码如下：
```python
    # forms.py
    class MessageBoardForm(forms.Form):
        title = forms.CharField(max_length=3,label='标题',min_length=2,error_messages={"min_length":'标题字符段不符合要求！'})
        content = forms.CharField(widget=forms.Textarea,label='内容')
        email = forms.EmailField(label='邮箱')
        reply = forms.BooleanField(required=False,label='回复')
```
每个Django表单的实例都有一个内置的`is_valid()`方法，用来验证接收的数据是否合法。如果所有数据都合法，那么该方法将返回True，并将所有的表单数据转存到它的一个叫做`cleaned_data`的属性中，该属性是以个字典类型数据。

2. 视图处理
然后在视图中，根据是 `GET`还是 `POST`请求来做相应的操作。如果是 `GET`请求，那么返回一个空的表单，如果是 `POST`请求，那么将提交上来的数据进行校验。示例代码如下：
```python
    # views.py
    class IndexView(View):
        def get(self,request):
            form = MessageBoardForm()
            return render(request,'index.html',{'form':form})
            
        def post(self,request):
            form = MessageBoardForm(request.POST)
            if form.is_valid():
                title = form.cleaned_data.get('title')
                content = form.cleaned_data.get('content')
                email = form.cleaned_data.get('email')
                reply = form.cleaned_data.get('reply')
                return HttpResponse('success')
            else:
                print(form.errors)
                return HttpResponse('fail')
```
3. 模板处理
在使用 `GET`请求的时候，我们传了一个 `form`给模板，那么以后模板就可以使用 `form`来生成一个表单的 `html`代码。在使用 `POST`请求的时候，我们根据前端上传上来的数据，构建一个新的表单，这个表单是用来验证数据是否合法的，如果数据都验证通过了，那么我们可以通过 `cleaned_data`来获取相应的数据。在模板中渲染表单的 HTML 代码如下：
```html
    <form action="" method="post">
        <table>
            {{ form.as_table }}
            <tr>
                <td></td>
                <td><input type="submit" value="提交"></td>
            </tr>
        </table>
    </form>
```
要点：

    + `<form>...</form>`标签要自己写；
    + 使用POST的方法时，必须添加`{% csrf_token %}`标签，用于处理csrf安全机制；
{{ form }}代表Django为你生成其它所有的form标签元素，也就是我们上面做的事情；
提交按钮需要手动添加！
提示：默认情况下，Django支持HTML5的表单验证功能，比如邮箱地址验证、必填项目验证等等
一定要**注意**，它不包含`<form>`标签本身以及提交按钮！！！为什么要这样？方便你自己控制表单动作和CSS，JS以及其它类似bootstrap框架的嵌入！
我们在最外面给了一个 `form`标签，然后在里面使用了 `table`标签来进行美化，在使用 `form`对象渲染的时候，使用的是 `table`的方式，当然还可以使用 `ul`的方式（ `as_ul` ），也可以使用 `p`标签的方式（ `as_p`），并且在后面我们还加上了一个提交按钮。这样就可以生成一个表单了

### 高级技巧

每一个表单字段类型都对应一种`Widget`类，每一种`Widget`类都对应了HMTL语言中的一种`input`元素类型，比如`<input type="text">`。需要在HTML中实际使用什么类型的input，就需要在Django的表单字段中选择相应的field。比如要一个`<input type="text">`，可以选择一个`CharField`。

一旦你的表单接收数据并验证通过了，那么就可以从form.cleaned_data字典中读取所有的表单数据，下面是一个例子：
```python
# views.py

from django.core.mail import send_mail

if form.is_valid():
    subject = form.cleaned_data['subject']
    message = form.cleaned_data['message']
    sender = form.cleaned_data['sender']
    cc_myself = form.cleaned_data['cc_myself']

    recipients = ['info@example.com']
    if cc_myself:
        recipients.append(sender)

    send_mail(subject, message, sender, recipients)
    return HttpResponseRedirect('/thanks/')
```

### 使用表单模板

1 . 表单渲染格式
前面我们通过`{{ form }}`模板语言，简单地将表单渲染到HTML页面中了，实际上，有更多的方式：
    + `{{ form.as_table }}`将表单渲染成一个表格元素，每个输入框作为一个`<tr>`标签
    + `{{ form.as_p }}` 将表单的每个输入框包裹在一个`<p>`标签内 tags
    + `{{ form.as_ul }}` 将表单渲染成一个列表元素，每个输入框作为一个`<li>`标签

注意：你要自己手动编写`<table>`和`<ul>`标签。

下面是将上面的`ContactForm`作为`{{ form.as_p }}`的例子：
```html
<p><label for="id_subject">Subject:</label>
    <input id="id_subject" type="text" name="subject" maxlength="100" required /></p>
<p><label for="id_message">Message:</label>
    <textarea name="message" id="id_message" required></textarea></p>
<p><label for="id_sender">Sender:</label>
    <input type="email" name="sender" id="id_sender" required /></p>
<p><label for="id_cc_myself">Cc myself:</label>
    <input type="checkbox" name="cc_myself" id="id_cc_myself" /></p>
```
注意：Django自动为每个input元素设置了一个id名称，对应label的for参数。

2 . 手动渲染表单字段
直接`{{ form }}`虽然好，啥都不用操心，但是往往并不是你想要的，比如你要使用CSS和JS，比如你要引入Bootstarps框架，这些都需要对表单内的input元素进行额外控制，那怎么办呢？手动渲染字段就可以了。

可以通过`{{ form.name_of_field }}`获取每一个字段，然后分别渲染，如下例所示：
```html
{{ form.non_field_errors }}
<div class="fieldWrapper">
    {{ form.subject.errors }}
    <label for="{{ form.subject.id_for_label }}">Email subject:</label>
    {{ form.subject }}
</div>
<div class="fieldWrapper">
    {{ form.message.errors }}
    <label for="{{ form.message.id_for_label }}">Your message:</label>
    {{ form.message }}
</div>
<div class="fieldWrapper">
    {{ form.sender.errors }}
    <label for="{{ form.sender.id_for_label }}">Your email address:</label>
    {{ form.sender }}
</div>
<div class="fieldWrapper">
    {{ form.cc_myself.errors }}
    <label for="{{ form.cc_myself.id_for_label }}">CC yourself?</label>
    {{ form.cc_myself }}
</div>
```
其中的`label`标签甚至可以用`label_tag()`方法来生成，于是可以简写成下面的样子:
```html
<div class="fieldWrapper">
    {{ form.subject.errors }}
    {{ form.subject.label_tag }}
    {{ form.subject }}
</div>
```
这样子是不是更加灵活了呢？但是灵活的代价就是我们要写更多的代码，又偏向原生的HTML代码多了一点。

3 . 渲染表单错误信息：
注意上面的例子中，我们使用`{{ form.name_of_field.errors }}`模板语法，在表单里处理错误信息。对于每一个表单字段的错误，它其实会实际生成一个无序列表，参考下面的样子：
```html
<ul class="errorlist">
    <li>Sender is required.</li>
</ul>
```
这个列表有个默认的CSS样式类`errorlist`，如果你想进一步定制这个样式，可以循环错误列表里的内容，然后单独设置样式：
```html
{% if form.subject.errors %}
    <ol>
    {% for error in form.subject.errors %}
        <li><strong>{{ error|escape }}</strong></li>
    {% endfor %}
    </ol>
{% endif %}
```
一切非字段的错误信息，比如表单的错误，隐藏字段的错误都保存在`{{ form.non_field_errors }}`中，上面的例子，我们把它放在了表单的外围上面，它将被按下面的HTML和CSS格式渲染：
```html
<ul class="errorlist nonfield">
    <li>Generic validation error</li>
</ul>
```

4 . 循环表单的字段
如果你的表单字段有相同格式的HMTL表现，那么完全可以循环生成，不必要手动的编写每个字段，减少冗余和重复代码，只需要使用模板语言中的`{% for %}`循环，如下所示：
```html
{% for field in form %}
    <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
        {% if field.help_text %}
        <p class="help">{{ field.help_text|safe }}</p>
        {% endif %}
    </div>
{% endfor %}
```
下表是`{{ field }}`中非常有用的属性，这些都是Django内置的模板语言给我们提供的方便：

|属性|	说明|
|--- |---|
|`{{ field.label }}`|	字段对应的label信息|
|`{{ field.label_tag }}`|	自动生成字段的label标签，注意与`{{ field.label }}`的区别。|
|`{{ field.id_for_label }}`|	自定义字段标签的id|
|`{{ field.value }}`|	当前字段的值，比如一个`Email`字段的值`someone@example.com`|
|`{{ field.html_name }}`|	指定字段生成的`inpu`t标签中`name`属性的值|
|`{{ field.help_text }}`|	字段的帮助信息|
|`{{ field.errors }}`|	包含错误信息的元素|
|`{{ field.is_hidden }}`|	用于判断当前字段是否为隐藏的字段，如果是，返回True|
|`{{ field.field }}`|	返回字段的参数列表。例如`{{ char_field.field.max_length }}`|

5 . 不可见字段的特殊处理
很多时候，我们的表单中会有一些隐藏的不可见的字段，比如honeypot。我们需要让它在任何时候都仿佛不存在一般，比如有错误的时候，如果你在页面上显示了不可见字段的错误信息，那么用户会很迷惑，这是哪来的呢？所以，通常我们是不显示不可见字段的错误信息的。

Django提供了两种独立的方法，用于循环那些不可见的和可见的字段，`hidden_fields()`和`visible_fields()`。这里，我们可以稍微修改一下前面的例子：
```html
{# 循环那些不可见的字段 #}
{% for hidden in form.hidden_fields %}
{{ hidden }}
{% endfor %}
{# 循环可见的字段 #}
{% for field in form.visible_fields %}
    <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
    </div>
{% endfor %}
```

6 . 重用表单模板
如果你在自己的HTML文件中，多次使用同一种表单模板，那么你完全可以把表单模板存成一个独立的HTML文件，然后在别的HTML文件中通过include模板语法将其包含进来，如下例所示：
```html
# 实际的页面文件中:
{% include "form_snippet.html" %}

-----------------------------------------------------

# 单独的表单模板文件form_snippet.html:
{% for field in form %}
    <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
    </div>
{% endfor %}
```
如果你的页面同时引用了好几个不同的表单模板，那么为了防止冲突，你可以使用with参数，给每个表单模板取个别名，如下所示：
```html
{% include "form_snippet.html" with form=comment_form %}
```
在使用的时候就是：
```html
{% for field in comment_form %}
......
```
如果你经常做这些重用的工作，建议你考虑自定义一个内联标签，这已经是Django最高级的用法了。
