# 表单

## HTML中的表单

单纯从前端的 `html`来说，表单是用来提交数据给服务器的,不管后台的服务器用的是 `Django`还是 `PHP`语言还是其他语言。只要把 `input`标签放在 `form`标签中，然后再添加一个提交按钮，那么以后点击提交按钮，就可以将 `input`标签中对应的值提交给服务器了。

## Django中的表单

`Django`中的表单丰富了传统的 `HTML`语言中的表单。在 `Django`中的表单，主要做以下两件事：
1. 渲染表单模板。
2. 表单验证数据是否合法。

## Django中表单使用流程

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

