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
我们在最外面给了一个 `form`标签，然后在里面使用了 `table`标签来进行美化，在使用 `form`对象渲染的时候，使用的是 `table`的方式，当然还可以使用 `ul`的方式（ `as_ul` ），也可以使用 `p`标签的方式（ `as_p`），并且在后面我们还加上了一个提交按钮。这样就可以生成一个表单了