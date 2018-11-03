31. LOGGING
默认值：日志配置字典。

日志配置信息。

32. LOGGING_CONFIG
默认值：'logging.config.dictConfig'

用于在Django项目中配置日志记录的可调用项的路径。

33. MEDIA_ROOT
默认值：''（空字符串）

用户上传的文件，所在目录的，文件系统绝对路径。也就是指示上传文件放到哪里。

例如： "/var/www/example.com/media/"

警告：MEDIA_ROOT和STATIC_ROOT必须设置为不同的值。

34. MEDIA_URL
默认值：''（空字符串）

MEDIA_URL指向MEDIA_ROOT所指定的media文件，用来管理保存的文件。该URL设置为非空值时，必须以斜杠“/”结束。

若你打算在模版中使用{{ MEDIA_URL }}，必须在TEMPLATES的context_processors设置中添加django.template.context_processors.media。

警告:MEDIA_URL和STATIC_URL必须设置为不同的值。

35. MIDDLEWARE
默认值：None

Django1.10中的新功能。要使用的中间件列表。Django-admin命令创建的新项目中，settings.py文件里默认会为MIDDLEWARE配置项添加一系列Django内置的中间件，我们保持它不变就好了。

36. ROOT_URLCONF
默认：未指定

一个字符串，表示根URLconf的完整Python导入路径。例如："mydjangoapps.urls"。

每个请求都可以覆盖它，通过设置HTTP请求HttpRequest对象的urlconf属性。但不是闲的，请不要特别设置，使用默认值就好。

37. SECRET_KEY
默认值：''（空字符串）

当前Django项目实例的密钥。用于提供cryptographic签名，是一个唯一的并且不可预测的值。比如：

SECRET_KEY = '+$*n1_pkko%zaz3!lm8lg208@uj^qy3mcsuy+*ov%ikpvd5$rf'
通过django-admin startproject xxx命令创建的项目，会在settings.py中添加随机生成的SECRET_KEY。

如果未设置SECRET_KEY，Django将无法启动。

警告：不要透露该配置项的真实值！

38. TEMPLATES
默认值：[]（空列表）

Django模板系统相关的配置。列表中每一项都是一个字典类型数据（类似DATABASE配置），可以配置模板不同的功能。

下面是一个例子：

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'APP_DIRS': True,
    },
]
以下选项适用于所有后端。

BACKEND：

默认：未指定

要使用的模板后端。 内置模板后端有：

'django.template.backends.django.DjangoTemplates'
'django.template.backends.jinja2.Jinja2'
通过将BACKEND设置为完全限定路径（即'mypackage.whatever.Backend'），你可以使用第三方提供的模板后端。

NAME：

默认值：见下面

此特定模板引擎的别名，一个标识符，用于在某些情况下指定选择引擎进行渲染。别名在所有已配置的模板引擎中必须是唯一的。

当没有提供时，如果后端是'mypackage.whatever.Backend'，则其默认名称为'whatever'。

DIRS：

默认值：[]（空列表）

搜索模版的路径列表。搜索引擎会按照列表的排列顺序查找template资源文件。短路算法，找到即退出，不再往下找。

APP_DIRS

默认值：False

Templates搜索引擎是否应该在已安装的app中查找Template源文件。建议保持打开，即设置为True！

由django-admin startproject xxx命令创建的Django项目，其settings.py文件中'APP_DIRS'已经设置为True了。

OPTIONS：

默认值：{}

传递给模板后端的额外参数。 可用参数因模板后端而异。

39. TIME_ZONE
默认：'America/Chicago'

时区设置。

注意，这个配置项的值不一定要和服务器的时区一致。例如，一个服务器可上可能有多个Django站点，每个站点都有一个单独的时区设置。

如果要设为中国时间，也就是北京时间，请赋值：TIME_ZONE = 'Asia/Shanghai'。注意是上海，不是北京，囧！

当USE_TZ为False时，它将成为Django存储所有日期和时间数据时，使用的时区。 当USE_TZ为True 时，它是Django显示模板中的时间，解释表单中的日期，使用的时区。所以，通常我们都将USE_TZ同时设置为False！

注：在Windows 环境中，Django不能可靠地交替其它时区。如果你在Windows上运行Django，TIME_ZONE必须设置为与系统时区一致。

40. USE_I18N
默认值：True

这是一个布尔值，指定Django的翻译系统是否开启。如果设置为False，Django会做一些优化，不去加载翻译机制。

由django-admin startproject xxx命令创建的Django项目，其settings.py文件中'USE_I18N'已经设置为True了。

41. USE_L10N
默认值：False

一个布尔值，用于决定是否开启数据本地化。如果此设置为True，例如Django将使用当前语言环境的格式显示数字和日期。

由django-admin startproject xxx命令创建的Django项目，其settings.py文件中'USE_L10N'已经设置为True了。

42. USE_TZ
默认值：False

一个布尔值,用来指定是否使用指定的时区(TIME_ZONE)的时间。若为True, 则Django会使用内建的时区的时间；否则, Django将会使用本地的时间。

由django-admin startproject xxx命令创建的Django项目，其settings.py文件中'USE_TZ'已经设置为True了。

如果我们将TIME_ZONE设置成了Asia/Shanghai， 那么务必同时将USE_TZ改成False！

43. WSGI_APPLICATION
默认值：None

Django的内置服务器（例如runserver）将使用的WSGI应用程序对象的完整Python路径。

Django使用WSGI协议与外部进行通信。

由django-admin startproject xxx命令创建的Django项目，将自动创建一个简单的wsgi.py模块，里面有一个可调用的application变量，WSGI_APPLICATION配置项的值就指向这个application变量。

下面是wsgi.py的源码：

"""
WSGI config for mysite project.
It exposes the WSGI callable as a module-level variable named ``application``.
For more information on this file, see https://docs.djangoproject.com/en/1.11/howto/deployment/wsgi/
"""

import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings")

application = get_wsgi_application()
以下是一些子系统或工具框架的相关配置

44. CACHES
默认值：

{
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
    }
}
一个嵌套字典，包含所有缓存系统要使用的设置。

CACHES设置必须配置一个default缓存，还可以同时指定任何数量的附加缓存（也可以没有）。

以下是重要的内部配置项目：

    + BACKEND：

默认值：''（空字符串）

要使用的缓存后端。 内置高速缓存后端有：
```python
'django.core.cache.backends.db.DatabaseCache'
'django.core.cache.backends.dummy.DummyCache'
'django.core.cache.backends.filebased.FileBasedCache'
'django.core.cache.backends.locmem.LocMemCache'
'django.core.cache.backends.memcached.MemcachedCache'
'django.core.cache.backends.memcached.PyLibMCCache'
```
通过将BACKEND设置为缓存后端类的完全限定路径（例如mypackage.backends.whatever.WhateverCache），可以使用第三方的缓存后端。）。

    + LOCATION：

默认值：''（空字符串）

要使用的缓存的位置，可能是文件系统缓存的目录，内存缓存服务器的主机和端口，或者只是本地内存缓存的标识名称。 例如：
```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': '/var/tmp/django_cache',
    }
}

    + OPTIONS:

默认值：None

传递到缓存后端的额外参数。可用参数因缓存后端而异。

    + TIMEOUT:

默认值：300

高速缓存的有效时间。 如果此设置的值为None，则缓存将永远不会过期。

    + VERSION：

默认值：1

Django服务器生成的缓存键的默认版本号。

45. AUTHENTICATION_BACKENDS
默认值：['django.contrib.auth.backends.ModelBackend']

在尝试验证用户时使用的认证后端的列表。默认使用Django自带的Auth框架。

46. AUTH_USER_MODEL
默认值：'auth.User'

默认使用的User模型。

47. LOGIN_REDIRECT_URL
默认：'/accounts/profile/'

登录之后，如果contrib.auth.login视图找不到next参数，请求将被重定向到该URL。

48. LOGIN_URL
默认：'/accounts/login/'

登录页面的URL。

49. LOGOUT_REDIRECT_URL
默认值：None

使用LogoutView视图退出登录后，请求被重定向的URL。如果设置为None，则不执行重定向。

50. PASSWORD_RESET_TIMEOUT_DAYS
默认：3

重置密码的链接，的有效期，的天数。（逗号分开，是不是更好理解一点？） 用于django.contrib.auth的密码重置功能。

51. PASSWORD_HASHERS
密码哈希使用的算法。

默认：

[
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
    'django.contrib.auth.hashers.BCryptPasswordHasher',
]
在Django1.10时，以下hashers从默认值中删除：
```python
'django.contrib.auth.hashers.SHA1PasswordHasher'
'django.contrib.auth.hashers.MD5PasswordHasher'
'django.contrib.auth.hashers.UnsaltedSHA1PasswordHasher'
'django.contrib.auth.hashers.UnsaltedMD5PasswordHasher'
'django.contrib.auth.hashers.CryptPasswordHasher'
```
52. MESSAGE_LEVEL
默认值：messages.INFO

设置Django内置的消息框架-message，将记录的最低消息级别。

53. MESSAGE_STORAGE
默认值：'django.contrib.messages.storage.fallback.FallbackStorage'

控制Django在哪里存储消息数据。 有效值为：
```python
'django.contrib.messages.storage.fallback.FallbackStorage'
'django.contrib.messages.storage.session.SessionStorage'
'django.contrib.messages.storage.cookie.CookieStorage'
```
54. SESSION_COOKIE_AGE
默认：1209600（2个星期）

会话Cookie的过期时间，以秒为单位。

55. SESSION_COOKIE_NAME
默认值：'sessionid'

要用于会话的Cookie的名称。 名字随意，只要与应用程序中的其他cookie名称不同。

56. SESSION_ENGINE
默认值：'django.contrib.sessions.backends.db'

会话使用的后端，也就是会话数据的保存未知。 内置支持的引擎有：
```python
'django.contrib.sessions.backends.db'
'django.contrib.sessions.backends.file'
'django.contrib.sessions.backends.cache'
'django.contrib.sessions.backends.cached_db'
'django.contrib.sessions.backends.signed_cookies'
```
57. SESSION_EXPIRE_AT_BROWSER_CLOSE
默认值：False

是否在用户关闭浏览器时过期会话。

58. SITE_ID
默认：未指定

当前站点在django_site数据库表中的ID，一个整数，从1开始计数。

很多人都不知道Django是支持多站点同时运行的。

通常我们都只有一个站点，所以不关心这个选项。如果你同时运行了多个站点，那么每个app就得知道自己是为那个站点或哪些站点服务的，这就需要SITE_ID参数了。

59. STATIC_ROOT
默认值：None

在DEBUG设置为False时，也就是线上环境时，Django项目里的静态文件（js\css\plugins）会无法使用。这是，需要运行`python manage.py collectstatic`，将静态文件统一收集到一个目录下。STATIC_ROOT配置的就是该目录的绝对路径。

示例：`"/var/www/example.com/static/"`

这个目录，刚开始应该是一个空目录。

60. STATIC_URL
默认值：None

引用位于STATIC_ROOT中的静态文件时使用的网址。

示例："/static/"或"http://static.example.com/"

该URL设置为非空值时，必须以斜杠“/”结束。

61. STATICFILES_DIRS
默认值：[]（空列表）

定义额外的静态文件搜索地址。

例如：
```python
STATICFILES_DIRS = [
    "/home/special.polls.com/polls/static",
    "/home/polls.com/polls/static",
    "/opt/webfiles/common",
]
```
请注意，即使在Windows上（例如`"C:/Users/user/mysite/extra_static_content"`），这些路径也要使用Unix样式的正斜杠。

如果你实在分不清楚`MEDIA_ROOT、MEDIA_URL、STATIC_ROOT、STATIC_URL和STATICFILES_DIRS`的区别，下面是一个参考版的设置:
```python
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/1.11/howto/static-files/

STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "static"),
]
STATIC_ROOT = os.path.join(BASE_DIR, "all_static_files")


MEDIA_ROOT = os.path.join(BASE_DIR, 'media').replace("\\", "/")
MEDIA_URL = '/media/'
```