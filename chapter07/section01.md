## 会话Session

---

因为Internet的HTTP协议的特性，每一次来自于用户浏览器的请求(`request`)都是无状态的，独立的。通俗地说，就是无法保存用户状态，后台服务器根本就不知道当前请求和以前及以后请求是否来自同一用户。对于静态的网站，这可能不是个问题，而对于动态网站，尤其是京东、天猫、银行等购物或金融网站，无法识别用户并保持用户状态是致使的，根本就无法担任服务。可以尝试将浏览器的`cookie`功能关闭，你会发现将无法在京东登录和购物。

了为保持连接状态，网站会通过用户的浏览器在用户机器内被限定的硬盘位置中写入一些数据，也就是所谓的`cookie`。通过`cookie`可以保存一些诸如用户名、浏览记录、表单记录、登录和注销等各种数据。但是这种方式非常不安全，因为`cookie`保存在用户的机器上，如果`cookie`被伪造、篡改或删除，就会造成极大的安全威胁，因此，现代网站设计通常将`cookie`用来保存一些不重要的内容，实际的用户数据和状态还是以`session`会话的方式保存在服务器端。

`session`就是在服务器的`cookie`，将用户数据保存在服务器端，远比保存在用户端要安全、方便和快捷得多。
`session`依赖`cookie`，但与`cookie`不同的地方在于`session`将所有的数据都放在服务器端，用户浏览器的`cookie`中只会保存一个非明文的识别信息，比如哈希值。
`session`是大多数网站都具备的功能。Django为我们提供了一个通用的`session`框架，并且可以使用多种`session`数据的保存方式：
    + 保存在数据库中
    + 保存到缓存中
    + 保存到文件中
    + 保存到cookie中

通常情况，没有特别需求的话，请使用保存在数据中的方式，尽量不要保存到`cookie`内。
Django的session框架支持匿名会话，封装了`cookies`的发送和接收过程。`cookie`包含一个会话ID而不是数据本身（除非你使用的是基于后端的`cookie`）。
Django的会话框架完全地、唯一基于`cookie`。它不像PHP一样，把会话的ID放在`URL`中。那样不仅使得`URL`变得丑陋，还使得你的网站易于受到通过`Referer`头部进行窃取会话ID的攻击。

---


###  一、启用会话

Django通过一个内置的中间件来实现会话功能。要启用会话就要先启用中间件。编辑`settings.py`中的`MIDDLEWARE`设置，确保存在`django.contrib.sessions.middleware.SessionMiddleware`这一行。默认情况在新建的项目中它是存在的。

如果不想使用会话功能，那么在`settings.py`文件中，将`SessionMiddleware`从`MIDDLEWARE`中删除，将`django.contrib.sessions`从`INSTALLED_APPS`中删除就可以了。

---

###  二、配置会话引擎

默认情况下，Django将会话数据保存在数据库内，（通过使用`django.contrib.sessions.models.Session`模型）。当然，你也可以将数据保存在文件系统或缓存内。

1. 基于数据库的会话
确保`INSTALLED_APPS`设置了`django.contrib.sessions`。然后运行`python manage.py migrate`命令在数据库内创建`session`表。

2. 基于缓存的会话
从性能角度考虑，基于缓存的会话会更好一些，但是首先，你得先配置好你的缓存。如果你定义了多个缓存，Django将使用默认的那个。如果你想用其它的，请将`SESSION_CACHE_ALIAS`参数设置为那个缓存的名字。
配置好缓存后，你可以选择两种保存数据的方法：
    + 一是将`SESSION_ENGINE`设置为`django.contrib.sessions.backends.cache`，简单的对会话进行保存。但是这种方法不是很可靠，因为当缓存数据存满时将清除部分数据，或遇到缓存服务器重启时数据将丢失。
    + 为了数据安全，可以将`SESSION_ENGINE`设置为`django.contrib.sessions.backends.cached_db`。这种方式在每次缓存的时候会同时将数据在数据库内写一份。当缓存不可用时，会话会从数据库内读取数据。

两种方法都很迅速，但是第一种简单的缓存更快一些，因为它忽略了数据的持久性。如果你使用缓存+数据库的方式，还南非要对数据库进行配置。

3. 基于文件的会话
将`SESSION_ENGING`设置为`django.contrib.sessions.backends.file`，同时，你必须配置`SESSION_FILE_PATH`（默认使用`tempfile.gettempdir()`方法的返回值，就像`/tmp`目录），确保你的文件存储目录以及`Web`服务器对该目录具有读定权限。

4. 基于cookie的会话
将`SESSION_ENGINE`设置为"`django.contrib.sessions.backends.signed_cookies`"。Django将使用加密签名工具和安全秘钥设置保存会话的cookie。

注意：建议将`SESSION_COOKIE_HTTPONLY`设置为`True`，阻止`javascript`对会话数据的访问，提高安全性。


---

### 三、在视图中使用会话

当会话中间件启用后，传递给视图request参数的HttpRequest对象将包含一个session属性，这个属性的值是一个类似字典的对象。

你可以在视图的任何地方读写request.session属性，或者多次编辑使用它。
```python
class backends.base.SessionBase
        # 这是所有会话对象的基类，包含标准的字典方法:
        __getitem__(key)
            Example: fav_color = request.session['fav_color']
        __setitem__(key, value)
            Example: request.session['fav_color'] = 'blue'
        __delitem__(key)
            Example: del request.session['fav_color']  # 如果不存在会抛出异常
        __contains__(key)
            Example: 'fav_color' in request.session
        get(key, default=None)
            Example: fav_color = request.session.get('fav_color', 'red')
        pop(key, default=__not_given)
            Example: fav_color = request.session.pop('fav_color', 'blue')
```
```python
        # 类似字典数据类型的内置方法
        keys()
        items()
        setdefault()
        clear()


        # 它还有下面的方法：
        flush()
            # 删除当前的会话数据和会话cookie。经常用在用户退出后，删除会话。

        set_test_cookie()
            # 设置一个测试cookie，用于探测用户浏览器是否支持cookies。由于cookie的工作机制，你只有在下次用户请求的时候才可以测试。
        test_cookie_worked()
            # 返回True或者False，取决于用户的浏览器是否接受测试cookie。你必须在之前先调用set_test_cookie()方法。
        delete_test_cookie()
            # 删除测试cookie。
        set_expiry(value)
            # 设置cookie的有效期。可以传递不同类型的参数值：
        • 如果值是一个整数，session将在对应的秒数后失效。例如request.session.set_expiry(300) 将在300秒后失效.
        • 如果值是一个datetime或者timedelta对象, 会话将在指定的日期失效
        • 如果为0，在用户关闭浏览器后失效
        • 如果为None，则将使用全局会话失效策略
        失效时间从上一次会话被修改的时刻开始计时。

        get_expiry_age()
            # 返回多少秒后失效的秒数。对于没有自定义失效时间的会话，这等同于SESSION_COOKIE_AGE.
            # 这个方法接受2个可选的关键字参数
        • modification:会话的最后修改时间（datetime对象）。默认是当前时间。
        •expiry: 会话失效信息，可以是datetime对象，也可以是int或None

        get_expiry_date()
            # 和上面的方法类似，只是返回的是日期

        get_expire_at_browser_close()
            # 返回True或False，根据用户会话是否是浏览器关闭后就结束。

        clear_expired()
            # 删除已经失效的会话数据。
        cycle_key()
            # 创建一个新的会话秘钥用于保持当前的会话数据。django.contrib.auth.login() 会调用这个方法。
```
1 . 序列化会话
Django默认使用JSON序列化会话数据。你可以在`SESSION_SERIALIZER`设置中自定义序列化格式，甚至写入警告说明。但是强烈建议你还是使用`JSON`，尤其是以`cookie`的方式进行会话时。

举个例子，一个使用`pickle`序列化会话数据的攻击场景。如果你使用的是已签名的Cookie会话并且`SECRET_KEY`被攻击者知道了（通过其它手段），攻击者就可以在会话中插入一个字符串，在`pickle`反序列化时，可以在服务器上执行危险的代码。在因特网上这个攻击技术很简单并很容易使用。尽管`Cookie`会话会对数据进行签名以防止篡改，但是`SECRET_KEY`的泄漏却使得一切前功尽弃。
    + 内置的序列化方法：
        + 1.class serializers.JSONSerializer。对django.core.signing中JSON序列化方法的一个包装。只可以序列化基本的数据类型。另外，JSON只支持以字符串作为键值，使用其它的类型会导致异常。
        ```python
            >>> # initial assignment
            >>> request.session[0] = 'bar'
            >>> # subsequent requests following serialization & deserialization
            >>> # of session data
            >>> request.session[0] # KeyError
            >>> request.session['0']
            'bar'
        ```
        同样，无法被JSON编码的，例如非UTF8格式的字节’\xd9’一样是无法被保存的，它会导致UnicodeDecodeError异常。
        + 2. class serializers.PickleSerializer。支持任意类型的Python对象，但是就像前面说的，可能导致远端执行代码的漏洞，如果攻击者知道了`SECRET_KEY`。
    + 自定义序列化方法
    
    自定义的序列化类必须分别实现dumps(self, obj)和loads(self, data)方法，用来实现序列化和反序列化会话数据字典。
        
2 . 会话使用中的一些建议
    + 使用普通的Python字符串作为request.session字典的键值。这不是一条硬性规则而是为方便起见。
    + 以一个下划线开始的会话字典的键被Django保留作为内部使用。
    + 不要用新对象覆盖request.session，不要直接访问或设置它的属性。像一个Python字典一样的使用它。
    
3 . 会话使用范例
下面这个简单的视图在用户发表评论后，在session中设置一个`has_commented`变量为True。它不允许用户重复发表评论。
```python
def post_comment(request, new_comment):
    if request.session.get('has_commented', False):
        return HttpResponse("You've already commented.")
    c = comments.Comment(comment=new_comment)
    c.save()
    request.session['has_commented'] = True
    return HttpResponse('Thanks for your comment!')
```
下面是一个简单的用户登录视图：
```python
def login(request):
    m = Member.objects.get(username=request.POST['username'])
    if m.password == request.POST['password']:
        request.session['member_id'] = m.id
        return HttpResponse("You're logged in.")
    else:
        return HttpResponse("Your username and password didn't match.")
```
下面则是一个退出登录的视图，与上面的相关：
```python
def logout(request):
    try:
        del request.session['member_id']
    except KeyError:
        pass
    return HttpResponse("You're logged out.")
```
Django内置的`django.contrib.auth.logout()`函数实际上所做的内容比上面的例子要更严谨，以防止意外的数据泄露，它会调用`request.session`的`flush()`方法。我们使用这个例子只是演示如何利用会话对象来工作，而不是一个完整的`logout()`实现。

---

### 四、测试cookie

为了方便，Django 提供一个简单的方法来测试用户的浏览器是否接受Cookie。只需在一个视图中调用`request.session`的`set_test_cookie()`方法，并在随后的视图中调用`test_cookie_worked()`获取测试结果（True或False)。**注意**，不能在同一个视图中调用这两个方法。

造成这种分割调用的原因是`cookie`的工作机制。当你设置一个`cookie`时，你无法立刻得到结果，直到浏览器发送下一个请求时才能获得结果。

在测试后，记得使用`delete_test_cookie()`方法清除测试数据。

下面是一个典型的范例：
```python
from django.http import HttpResponse
from django.shortcuts import render

def login(request):
    if request.method == 'POST':
        if request.session.test_cookie_worked():
            request.session.delete_test_cookie()
            return HttpResponse("You're logged in.")
        else:
            return HttpResponse("Please enable cookies and try again.")
    request.session.set_test_cookie()
    return render(request, 'foo/login_form.html')
```

---

### 五、在视图外使用session

在下面的例子中，我们直接从`django.contrib.sessions.backends.db`中导入了`SessionStore`对象。在你的实际代码中，应该采用下面的导入方法，根据`SESSION_ENGINE`的设置进行导入，如下所示：
```python
>>> from importlib import import_module
>>> from django.conf import settings
>>> SessionStore = import_module(settings.SESSION_ENGINE).SessionStore
```
在视图外可以通过下面的API操作会话数据：
```python
>>> from django.contrib.sessions.backends.db import SessionStore
>>> s = SessionStore()
>>> # stored as seconds since epoch since datetimes are not serializable in JSON.
>>> s['last_login'] = 1376587691
>>> s.create()
>>> s.session_key
'2b1189a188b44ad18c35e113ac6ceead'
>>> s = SessionStore(session_key='2b1189a188b44ad18c35e113ac6ceead')
>>> s['last_login']
1376587691
```
`SessionStore.create()`用于创建一个新的会话。`save()`方法用于保存一个已经存在的会话。`create`方法会调用`save`方法并循环直到生成一个未使用的`session_key`。直接调用`save`方法也可以创建一个新的会话，但在生成`session_key`的时候有可能和已经存在的发生冲突。

如果你使用的是`django.contrib.sessions.backends.db`模式，那么每一个会话其实就是一个普通的Django模型，你可以使用普通的Django数据库API访问它。会话模型的定义在`django/contrib/sessions/models.py`文件里。例如：
```python
>>> from django.contrib.sessions.models import Session
>>> s = Session.objects.get(pk='2b1189a188b44ad18c35e113ac6ceead')
>>> s.expire_date
datetime.datetime(2005, 8, 20, 13, 35, 12)
```
注意，需要调用get_decoded()方法才能获得会话字典，因为字典是采用编码格式保存的。如下：
```python
>>> s.session_data
'KGRwMQpTJ19hdXRoX3VzZXJfaWQnCnAyCkkxCnMuMTExY2ZjODI2Yj...'
>>> s.get_decoded()
{'user_id': 42}
```

---

### 六、会话的保存机制

默认情况下，只有当会话字典的某个值被重新设置或删除的时候，Django才会将会话内容保存到会话数据库内。
```python
# 会话被修改
request.session['foo'] = 'bar'
# 会话被删除
del request.session['foo']
# 会话被修改
request.session['foo'] = {}
# 会话没有被修改，只是修改了request.session['foo']
request.session['foo']['bar'] = 'baz'
```
要理解上面最后一种情况有点费劲。我们可以通过设置会话对象的modified属性值，显式地告诉会话对象它已经被修改过：`request.session.modified = True`。

要改变上面的默认行为，将`SESSION_SAVE_EVERY_REQUEST`设置为True，那么每一次单独的请求过来，Django都会保存会话到数据库。

注意，会话的Cookie只有在一个会话被创建或修改后才会再次发送。如果`SESSION_SAVE_EVERY_REQUEST`为True，每次请求都会发送cookie。

类似地，会话Cookie的失效部分在每次发送会话Cookie时都会更新。

如果响应的状态码为500，则会话不会被保存。

---

### 七、会话生存期

默认情况下，`SESSION_EXPIRE_AT_BROWSER_CLOSE`设置为False，也就是说`cookie`保存在用户的浏览器内，直到失效日期，这样用户就不必每次打开浏览器后都要再登录一次。

相反的，如果将`SESSION_EXPIRE_AT_BROWSER_CLOSE`设置为True，则意味着浏览器一关闭，`cookie`就失效，每次重新打开浏览器，你就得重新登录。

这个设置是一个全局的默认值，可以通过显式地调`request.session`的`set_expiry()`方法来覆盖，前面我们已经描述过了。

注意：有些浏览器（比如Chrome）具有在关闭后重新打开浏览器，会话依然保持的功能。这会与Django的`SESSION_EXPIRE_AT_BROWSER_CLOSE`设置发生冲突。请一定要小心。

---

### 八、清除已保存的会话

随着用户的访问，会话数据会越来越庞大。如果你使用的是数据库保存模式，那么`django_session`表的内容会逐渐增长。如果你使用的是文件模式，那么你的临时目录内的文件数量会不断增加。

造成这个问题的原因是，如果用户手动退出登录，Django将会自动删除会话数据，但是如果用户不退出登录，那么对应的会话数据不会被删除。

Django没有提供自动清除失效会话的机制。因此，你必须自己完成这项工作。但是Django提供了一个命令`clearsessions`用于清除会话数据，建议你基于这个命令设置一个周期性的自动清除机制，比如`crontab`或者`Windows`的调度任务。

不同的是，使用缓存模式的会话不需要你清理数据，因为缓存系统自己有清理过期数据的机制。使用cookie模式的会话也不需要，因为数据都存在用户的浏览器内，不用你帮忙。

---

### 下面是Django的session相关设置，用于帮助你控制会话的行为，大多数在前面都介绍过了：
    + SESSION_CACHE_ALIAS
    + SESSION_COOKIE_AGE
    + SESSION_COOKIE_DOMAIN
    + SESSION_COOKIE_HTTPONLY
    + SESSION_COOKIE_NAME
    + SESSION_COOKIE_PATH
    + SESSION_COOKIE_SECURE
    + SESSION_ENGINE
    + SESSION_EXPIRE_AT_BROWSER_CLOSE
    + SESSION_FILE_PATH
    + SESSION_SAVE_EVERY_REQUEST
    + SESSION_SERIALIZER
    
---

### 十、SessionStore对象

在会话内部，Django使用一个与会话引擎对应的会话保存对象。这个会话保存对象命名为`SessionStore`，位于`SESSION_ENGINE`设置指定的模块内。

所有Django支持的`SessionStore`类都继承`SessionBase`类，并实现了下面的数据操作方法：
    + exists()
    + create()
    + save()
    + delete()
    + load()
    + clear_expored()
    
为了创建一个自定义会话引擎或修改一个现成的引擎，你需要创建一个新的类，它继承SessionBase类或任何其他已经存在的SessionStore类。