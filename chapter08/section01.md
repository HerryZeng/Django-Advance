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

## 中间件

中间件是在`request`和`response`处理过程中的一个插件。比如在`request`到达视图函数之前，我们可以使用中间件来做一些相关的事情，比如可以判断当前这个用户有没有登录，如果登录了，就绑定一个`user`对象到`request`上。也可以在`response`到达浏览器之前，做一些相关的处理，比如想要统一在`response`上设置一些`cookie`信息等。

### 自定义中间件

中间件所处的位置没有规定。只要是放到项目当中即可。一般分为两种情况，如果中间件是属于某个`app`的，那么可以在这个`app`下面创建一个`python`文件用来存放这个中间件，也可以专门创建一个`Python`包，用来存放本项目的所有中间件。创建中间件有两种方式，一种是使用函数，一种是使用类，接下来对这两种方式做个介绍：

### 使用函数的中间件

```python
    def simple_middleware(get_response):
      # 这个中间件初始化的代码

        def middleware(request):
            # request到达view的执行代码

            response = get_response(request)

            # response到达浏览器的执行代码

            return response

        return middleware
```

### 使用类的中间件

```python
    class SimpleMiddleware(object):
      def __init__(self, get_response):
          self.get_response = get_response
          # 这个中间件初始化的代码
          def __call__(self, request):
              # request到达view之前执行的代码

              response = self.get_response(request)

              # response到达用户浏览器之前执行的代码

              return response
```

在写完中间件后，还需要在`settings.MIDDLEWARES`中配置写好的中间件才可以使用。比如我们写了一个在`request`到达视图函数之前，判断这个用户是否登录，如果已经登录就绑定一个`user`对象到`request`上的中间件，这个中间件放在当前项目的`middlewares.users`下：
```python
    def user_middleware(get_response):
      # 这个中间件初始化的代码

      def middleware(request):
          # request到达view的执行代码
          userid = request.session.get("userid")
          userModel = FrontUser.objects.filter(pk=userid).first()
          if userModel:
                  setattr(request,'frontuser',userModel)

          response = get_response(request)

          # response到达浏览器的执行代码

          return response

      return middleware
```