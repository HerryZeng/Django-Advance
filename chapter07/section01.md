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
