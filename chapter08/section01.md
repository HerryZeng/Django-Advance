## 上下文处理器

上下文处理器是可以返回一些数据，在全局模板中都可以使用。比如登录后的用户信息，在很多页面中都需要使用，那么我们可以放在上下文处理器中，就没有必要在每个视图函数中都返回这个对象。

在`settings.TEMPLATES.OPTIONS.context_processors`中，有许多内置的上下文处理器。这些上下文处理器的作用如下：
1. `django.template.context_processors.debug`：增加一个`debug`和`sql_queries`变量。在模板中可以通过他来查看到一些数据库查询。
2. `django.template.context_processors.request`：增加一个request变量。这个request变量也就是在视图函数的第一个参数。
3. `django.contrib.auth.context_processors.auth`：Django有内置的用户系统，这个上下文处理器会增加一个user对象。
4. `django.contrib.messages.context_processors.messages`：增加一个messages变量。
5. `django.template.context_processors.media`：在模板中可以读取MEDIA_URL。比如想要在模板中使用上传的文件，那么这时候就需要使用settings.py中设置的MEDIA_URL来拼接url。示例代码如下：
```html
    <img src="" />
```
6. `django.template.context_processors.static`：在模板中可以使用STATIC_URL。
7. `django.template.context_processors.csrf`：在模板中可以使用csrf_token变量来生成一个`csrf token`。

### 自定义上下文处理器

有时候我们想要返回自己的数据。那么这时候我们可以自定义上下文处理器。自定义上下文处理器的步骤如下：
1. 你可以根据这个上下文处理器是属于哪个`app`，然后在这个`app`中创建一个文件专门用来存储上下文处理器。比如`context_processors.py`。或者是你也可以专门创建一个`Python`包，用来存储所有的上下文处理器。
2. 在你定义的上下文处理器文件中，定义一个函数，这个函数只有一个request参数。这个函数中处理完自己的逻辑后，把需要返回给模板的数据，通过字典的形式返回。如果不需要返回任何数据，那么也必须返回一个空的字典。示例代码如下：
```python
    def frontuser(request):
        userid = request.session.get("userid")
        userModel = models.FrontendUser.objects.filter(pk=userid).first()
        if userModel:
          return {'frontuser':userModel}
        else:
          return {}
```