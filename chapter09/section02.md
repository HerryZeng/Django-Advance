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