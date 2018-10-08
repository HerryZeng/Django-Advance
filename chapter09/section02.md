## XSS攻击

`XSS（Cross Site Script）`攻击又叫做跨站脚本攻击。他的原理是用户在使用具有XSS漏洞的网站的时候，向这个网站提交一些恶意的代码，当用户在访问这个网站的某个页面的时候，这个恶意的代码就会被执行，从而来破坏网页的结构，获取用户的隐私信息等。

### XSS攻击场景

比如`A网站`有一个发布帖子的入口，如果用户在提交数据的时候，提交了一段`js`代码比如：`<script>alert("hello world");</script>`，然后A网站在渲染这个帖子的时候，直接把这个代码渲染了，那么这个代码就会执行，会在浏览器的窗口中弹出一个模态对话框来显示hello world！如果攻击者能成功的运行以上这么一段`js`代码，那他能做的事情就有很多很多了！

### XSS攻击防御

1. 如果不需要显示一些富文本，那么在渲染用户提交的数据的时候，直接进行转义就可以了。在`Django`的模板中默认就是转义的。也可以把数据在存储到数据库之前，就转义再存储进去，这样以后在渲染的时候，即使不转义也不会有安全问题，示例代码如下：
```python
    from django.template.defaultfilters import escape
    from .models import Comment
    from django.http import HttpResponse
    
    def comment(request):
        content = request.POST.get("content")
        escaped_content = escape(content)
        Comment.objects.create(content=escaped_content)
        return HttpResponse('success')
```
2. 如果对于用户提交上来的数据包含了一些富文本（比如：给字体换色，字体加粗等），那么这时候我们在渲染的时候也要以富文本的形式进行渲染，也即需要使用`safe`过滤器将其标记为安全的，这样才能显示出富文本样式。但是这样又会存在一个问题，如果用户提交上来的数据存在攻击的代码呢，那将其标记为安全的肯定是有问题的。示例代码如下：
```python
    # views.py
    
    def index(request):
        message = "<span style='color:red;'>红色字体</span><script>alert('hello world');</script>";
        return render_template(request,'index.html',context={"message":message})
```
那么这时候该怎么办呢？这时候我们可以指定某些标签我们是需要的（比如：span标签），而某些标签我们是不需要的（比如：script）那么我们在服务器处理数据的时候，就可以将这些需要的标签保留下来，把那些不需要的标签进行转义，或者干脆移除掉，这样就可以解决我们的问题了。这个方法是可行的，包括很多线上网站也是这样做的，在`Python`中，有一个库可以专门用来处理这个事情，那就是`sanitizer`。接下来讲下这个库的使用。

### bleach库

`bleach`库是用来清理包含`html`格式字符串的库。他可以指定哪些标签需要保留，哪些标签是需要过滤掉的。也可以指定标签上哪些属性是可以保留，哪些属性是不需要的。想要使用这个库，可以通过以下命令进行安装：
```python
    pip install bleach
```
这个库最重要的一个方法是`bleach.clean`方法，`bleach.clean`示例代码如下:
```python
    import bleach
    from bleach.sanitizer import ALLOWED_TAGS,ALLOWED_ATTRIBUTES

    @require_http_methods(['POST'])
    def message(request):
        # 从客户端中获取提交的数据
        content = request.POST.get('content')
    
        # 在默认的允许标签中添加img标签
        tags = ALLOWED_TAGS + ['img']
        # 在默认的允许属性中添加src属性
        attributes = {**ALLOWED_ATTRIBUTES,'img':['src']}
    
        # 对提交的数据进行过滤
        cleaned_content=bleach.clean(content,tags=tags,attributes=attributes)
```
相关介绍如下
1. `tags`：表示允许哪些标签。
2. `attributes`：表示标签中允许哪些属性。
3. `ALLOWED_TAGS`：这个变量是`bleach`默认定义的一些标签。如果不符合要求，可以对其进行增加或者删除。
4. `ALLOWED_ATTRIBUTES`：这个变量是`bleach`默认定义的一些属性。如果不符合要求，可以对其进行增加或者删除。

bleach更多资料：
github地址： [https://github.com/mozilla/bleach/](https://github.com/mozilla/bleach
)
文档地址： [https://bleach.readthedocs.io/](https://bleach.readthedocs.io/)