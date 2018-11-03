## 核心配置项

Django的默认配置文件中，包含上百条配置项目，其中很多是我们‘一辈子’都不碰到或者不需要单独配置的，这些项目在需要的时候再去查手册。

强调：配置的默认值不是在`settings.py`文件中！不要以为`settings.py`中的配置值就是默认值，参考前文。`settings.py`是使用`django-admin startproject xxx`命令时，额外给我们创建的。

下面介绍的是61个相对比较常用和重要的配置项，按字母顺序排序，但是最后部分是`cache、auth、message、session、static`等的配置。

|配置项|说明|
|---|---|
|ADMINS|代码错误通知的人的邮件地址列表|
|ALLOWED_HOSTS|该配置项列表中包含的是Django站点可以为之提供服务的主机/域名|
|APPEND_SLASH|请求重定向到以请求URL加/的URL地址|
|DATABASES|连接的数据库配置项|
|DATE_FORMAT|系统中日期字段的默认显示格式|
|DATE_INPUT_FORMATS|日期字段中输入数据时将能够被接受的格式列表|
|DATETIME_FORMAT|系统中显示datetime字段的默认格式|
|DATETIME_INPUT_FORMATS|在datetime字段中输入数据时将被接受的格式列表|
|DEBUG|打开/关闭调试模式|
|DEFAULT_CHARSET|HttpResponse响应对象的默认字符集|
|DEFAULT_CONTENT_TYPE|HttpResponse对象的默认内容类型|
|DEFAULT_FROM_EMAIL|默认的电子邮件发送地址，即发送方|
|DISALLOWED_USER_AGENTS|正则表达式对象的列表,代表哪些不允许访问任何页面的User-Agent字符串|
|EMAIL_BACKEND|用于发送邮件的后端|
|EMAIL_FILE_PATH|邮件后端保存输出文件时使用的目录|
|EMAIL_HOST|发送邮件使用的SMTP主机地址|
|EMAIL_HOST_PASSWORD|SMTP服务器使用的密码|
|EMAIL_HOST_USER|SMTP服务器使用的用户名|
|EMAIL_PORT|SMTP服务器使用的端口|
|EMAIL_SUBJECT_PREFIX|使用`django.core.mail.mail_admins`或`django.core.mail.mail_managers`发送的电子邮件的主题行前缀|
|EMAIL_USE_TLS|是否使用TLS(安全)与SMTP服务器连接|
|EMAIL_USE_SSL|SMTP服务器通信时是否使用SSL|
|EMAIL_SSL_CERTFILE|指定要用于SSL连接的PEM格式的证书链文件的路径|
|EMAIL_SSL_KEYFILE|指定要用于SSL连接的PEM格式的私钥文件的路径|
|EMAIL_TIMEOUT|邮件发送超时时间|
|FILE_CHARSET|从磁盘读取文件时使用的字符编码|
|INSTALLED_APPS|app列表|
|LANGUAGE_CODE|当前项目所使用的语言|
|LANGUAGES|可用的语言种类|
|LOCALE_PATHS|Django查找翻译文件的目录列表|
|LOGGING|日志配置信息|
|LOGGING_CONFIG|配置日志记录的可调用项的路径|
|MEDIA_ROOT|指示上传文件放到哪里|
|MEDIA_URL|指向`MEDIA_ROOT`所指定的media文件，用来管理保存的文件|
|MIDDLEWARE|要使用的中间件列表|
|ROOT_URLCONF|一个字符串，表示根URLconf的完整Python导入路径|
|SECRET_KEY|当前Django项目实例的密钥|
|TEMPLATES|Django模板系统相关的配置|
|TIME_ZONE|时区设置|
|USE_I18N|这是一个布尔值，指定Django的翻译系统是否开启|
|USE_L10N|一个布尔值，用于决定是否开启数据本地化|
|USE_TZ|一个布尔值,用来指定是否使用指定的时区|
|WSGI_APPLICATION|使用的WSGI应用程序对象的完整Python路径|
|CACHES|CACHES设置|
|AUTHENTICATION_BACKENDS|尝试验证用户时使用的认证后端的列表|
|AUTH_USER_MODEL|默认使用的User模型|
|LOGIN_REDIRECT_URL|登录之后，如果contrib.auth.login视图找不到next参数，请求将被重定向到该URL|
|LOGIN_URL|登录页面的URL|
|LOGOUT_REDIRECT_URL|使用LogoutView视图退出登录后，请求被重定向的URL|
|PASSWORD_RESET_TIMEOUT_DAYS|重置密码的链接，的有效期，的天数|
|PASSWORD_HASHERS|密码哈希使用的算法|
|MESSAGE_LEVEL|设置Django内置的消息框架|
|MESSAGE_STORAGE|控制Django在哪里存储消息数据|
|SESSION_COOKIE_AGE|会话Cookie的过期时间|
|SESSION_COOKIE_NAME|要用于会话的Cookie的名称|
|SESSION_ENGINE|会话使用的后端引擎|
|SESSION_EXPIRE_AT_BROWSER_CLOSE|是否在用户关闭浏览器时过期会话|
|SITE_ID|当前站点在django_site数据库表中的ID|
|STATIC_ROOT|线上环境静态文件的绝对路径|
|STATIC_URL|静态文件时使用的网址|
|STATICFILES_DIRS|定义额外的静态文件搜索地址|

1 . ADMINS
默认值：[]（空列表）

所有获得代码错误通知的人的邮件地址列表。当DEBUG=False，并且一个视图引发了异常时,Django将会给这个列表里的人发一封含有完整异常信息的电子邮件。列表中的每个项目都应该是（全名，电子邮件地址）的元组。例如：
```python
[('John', 'john@example.com'), ('Mary', 'mary@example.com')]
```
2 . ALLOWED_HOSTS
默认值：[]（空列表）

这是新手比较困惑的一个配置项。**该配置项列表中包含的是Django站点可以为之提供服务的主机/域名**。 也就是哪些主机或IP能够访问Django服务器！列表里的所有元素是共同存在的关系，不纯在冲突、优先级和排斥的关系。

列表中的值可以是`localhost`、`www.example.com`或者`.example.com`形式的域名。
也可以是IP地址，比如：`137.2.4.1`、`192.168.1.1`、`0.0.0.0`、`127.0.0.1`
还可以是通配符`'*'`，表示所有外部主机都可以访问Django。但这种情况具有安全风险，在线上环境不要使用。

对于`0.0.0.0`，表示局域网内的主机都可以访问Django。
当`DEBUG`为`True`和`ALLOWED_HOSTS`为空时，默认相当于配置：`['localhost'， '127.0.0.1'， '[:: 1]']`。
3. APPEND_SLASH
默认值：True

当设定为True时，如果请求的URL没有匹配到URLconf里面的任何一条URL路由设置，并且没有以/（斜杠）结束，该请求将重定向到以请求URL加/的URL地址。**需要注意的是重定向有可能导致POST提交的数据丢失**。

通俗的解释就是，如果你在写url时忘记了在最后添加一个斜杠，Django会默认帮你加上！请尽量保持默认值！

`APPEND_SLASH`设置只有在安装了`CommonMiddleware`中间件时才会启用。
4. DATABASES
默认值: {} (空的字典)

该配置项包含Django项目使用的所有数据库的设置。这是一个嵌套字典。

DATABASES设置必须配置一个`default`数据库，以及指定任意数量的其它数据库（可以没有）。

最简单的配置方式是使用SQLite数据库，Python和Django内置对SQLite数据库的支持，无需任何额外的安装和插件，如下所示：
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'mydatabase',
        # 或者'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```
由于SQLite数据库就在Django项目本地，通常还是在Django项目根目录下，以一个文件的形式存在，没有用户名、密码、IP、port的问题，所以配置比较简单。

当连接其他数据库后端，比如MySQL、Oracle 或PostgreSQL，必须提供更多的连接参数。下面的例子用于PostgreSQL：
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```
下面是DATABASES的内部配置项：
    + ATOMIC_REQUESTS

默认值：False

原子性事务请求。

    + AUTOCOMMIT

默认值：True
自动提交。

    + ENGINE

默认值：''（空字符串）

最重要的配置项！指定使用的数据库后端。 内建的数据库后端名称有：
```python
'django.db.backends.postgresql'
'django.db.backends.mysql'
'django.db.backends.sqlite3'
'django.db.backends.oracle'
```

    + HOST

默认值：''（空字符串）
数据库所在的主机。设置的值可以是主机名、IP地址、和socket路径。空字符串表示localhost。SQLite不需要配置这个选项。
如果其值以斜杠（'/'）开头并且你使用的是MySQL，MySQL将通过Unix socket连接。 像这样：

"HOST": '/var/run/mysql'

    + NAME

默认值：''（空字符串）
使用的数据库名称。 对于SQLite，它是数据库文件的完整路径。指定路径时，请始终使用前向的斜杠，即使在Windows 上（例如C:/homes/user/mysite/sqlite3.db）。

但是对于MySQL等数据库，NAME指的是数据库系统中的具体某个database，Django没有能力自动在MySQL中创建数据库，它只能通过模型创建数据表。所以需要你通过各种数据客户端，在MySQL中提前创建好Django项目需要的数据库，使用命令`CREATE DATABASE mysite CHARACTER SET utf8;`，`mysite`是你的数据库名，务必同时指定字符编码方式为utf8！

    + CONN_MAX_AGE

默认：0
数据库连接的存活时间，以秒为单位。0表示在每个请求结束时关闭数据库连接，None表示无限的持久连接。

    + OPTIONS

Default: {} (默认为空的字典)
连接数据库时使用的额外参数。可用的参数与你的数据库后端有关。

    + PASSWORD

默认值：''（空字符串）
连接数据库时使用的密码。SQLite不需要这个选项。

    + PORT

默认值：''（空字符串）
连接数据库时使用的端口。空字符串表示默认的端口。SQLite不需要这个选项。 MySQL一般是3306。

    + TIME_ZONE

默认值：None
数据库中使用的时区。

    + USER

默认值：''（空字符串）
连接数据库时使用的用户名。SQLite不需要这个选项。

    + TEST

Default: {} (默认为空的字典)
测试数据库用的配置。

以下是测试数据库配置的示例：
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'USER': 'mydatabaseuser',
        'NAME': 'mydatabase',
        'TEST': {
            'NAME': 'mytestdatabase',
        },
    },
}
```
5 . DATE_FORMAT
默认值：'N j, Y' (例如：Feb. 4, 2003)

系统中日期字段的默认显示格式

6 . DATE_INPUT_FORMATS
默认值：
```python
[
    '%Y-%m-%d', '%m/%d/%Y', '%m/%d/%y', # '2006-10-25', '10/25/2006', '10/25/06'
    '%b %d %Y', '%b %d, %Y',            # 'Oct 25 2006', 'Oct 25, 2006'
    '%d %b %Y', '%d %b, %Y',            # '25 Oct 2006', '25 Oct, 2006'
    '%B %d %Y', '%B %d, %Y',            # 'October 25 2006', 'October 25, 2006'
    '%d %B %Y', '%d %B, %Y',            # '25 October 2006', '25 October, 2006'
]
```
日期字段中输入数据时将能够被接受的格式列表。格式将按顺序尝试，使用第一个有效的格式。

7 . DATETIME_FORMAT
默认值：'N j, Y, P' (例如Feb. 4, 2003, 4 p.m.)

系统中显示datetime字段的默认格式。
8. DATETIME_INPUT_FORMATS
默认值：
```python
[
    '%Y-%m-%d %H:%M:%S',     # '2006-10-25 14:30:59'
    '%Y-%m-%d %H:%M:%S.%f',  # '2006-10-25 14:30:59.000200'
    '%Y-%m-%d %H:%M',        # '2006-10-25 14:30'
    '%Y-%m-%d',              # '2006-10-25'
    '%m/%d/%Y %H:%M:%S',     # '10/25/2006 14:30:59'
    '%m/%d/%Y %H:%M:%S.%f',  # '10/25/2006 14:30:59.000200'
    '%m/%d/%Y %H:%M',        # '10/25/2006 14:30'
    '%m/%d/%Y',              # '10/25/2006'
    '%m/%d/%y %H:%M:%S',     # '10/25/06 14:30:59'
    '%m/%d/%y %H:%M:%S.%f',  # '10/25/06 14:30:59.000200'
    '%m/%d/%y %H:%M',        # '10/25/06 14:30'
    '%m/%d/%y',              # '10/25/06'
]
```
在datetime字段中输入数据时将被接受的格式列表。格式将按顺序尝试，使用第一个有效的格式。
9. DEBUG
默认值：False

打开/关闭调试模式。最重要的设置之一！默认值是`False`，你没有看错！只是在`settings.py`中又帮我们设置为True了，打开了调试模式，方便开发者和测试者的！线上部署网站的时候务必设置为`False`。

调试模式下可以显示错误页面的细节。若你的应用产生了一个异常，Django会显示追溯细节，包括许多环境变量的元数据, 比如所有当前定义的Django设置。

作为安全考虑，调试信息中不会列出包含下列关键字的配置项的内容，例如SECRET_KEY：
```python
'API'
'KEY'
'PASS'
'SECRET'
'SIGNATURE'
'TOKEN'
```
注意，这里使用的是包含匹配，也就是说只要出现这些子字符串的配置项将匹配上。例如'PASS'能够匹配 PASSWORD, 'TOKEN'也将匹配TOKENIZED等等.

最后再次强调，如果DEBUG为False，你还需要正确设置ALLOWED_HOSTS以及静态文件。错误设置将导致对所有的请求返回“Bad Request (400)”。
10. DEFAULT_CHARSET
默认值：'utf-8'

HttpResponse响应对象的默认字符集
11. DEFAULT_CONTENT_TYPE
默认值：'text/html'

HttpResponse对象的默认内容类型。
12. DEFAULT_FROM_EMAIL
默认值：'webmaster@localhost'

默认的电子邮件发送地址，即发送方。
13. DISALLOWED_USER_AGENTS
默认值：[]（空列表）

这是一个编译好了的正则表达式对象的列表。代表哪些不允许访问任何页面的User-Agent字符串。也就是说如果一个请求的User-Agent属性，被这个配置项中的任何一个正则表达式匹配到了，那么这个请求将被阻止。常用于对付机器人和网络蜘蛛。需要CommonMiddleware中间件支持。
14. EMAIL_BACKEND
默认值：'django.core.mail.backends.smtp.EmailBackend'

用于发送邮件的后端。
15. EMAIL_FILE_PATH
默认：未指定

邮件后端保存输出文件时使用的目录。
16. EMAIL_HOST
默认：'localhost'

发送邮件使用的主机。
17. EMAIL_HOST_PASSWORD
默认值：''（空字符串）

`EMAIL_HOST`的SMTP服务器使用的密码。
18. EMAIL_HOST_USER
默认值：''（空字符串）

EMAIL_HOST的SMTP服务器使用的用户名。
19. EMAIL_PORT
默认：25

EMAIL_HOST的SMTP服务器使用的端口
20. EMAIL_SUBJECT_PREFIX
默认值：'[Django] '

使用`django.core.mail.mail_admins`或`django.core.mail.mail_managers`发送的电子邮件的主题行前缀。
21. EMAIL_USE_TLS
默认值：False

是否使用TLS(安全)与SMTP服务器连接。用于显式TLS连接,通常在端口587上
22. EMAIL_USE_SSL
默认值：False

在与SMTP服务器通信时是否使用隐式TLS（安全）连接。在大多数电子邮件文档中，此类型的TLS连接称为SSL。 它通常在端口465上使用。

注意：腾讯家的qq邮箱服务，需要使用ssl安全链接在465端口上！

请注意，`EMAIL_USE_TLS`与`EMAIL_USE_SSL`是互斥的，因此只能将其中一个设置设置为`True`。
23. EMAIL_SSL_CERTFILE
默认值：None

如果`EMAIL_USE_SSL`或`EMAIL_USE_TLS`为True，则可以选择指定要用于SSL连接的PEM格式的证书链文件的路径
24. EMAIL_SSL_KEYFILE
默认值：None

如果`EMAIL_USE_SSL`或`EMAIL_USE_TLS`为True，可以选择指定要用于SSL连接的PEM格式的私钥文件的路径。
25. EMAIL_TIMEOUT
默认值：None

邮件发送超时时间。
26. FILE_CHARSET
默认值：'utf-8'

从磁盘读取文件时使用的字符编码。包括模板文件和初始SQL数据文件。

没有特别需求，请不要修改它。
27. INSTALLED_APPS
默认值：[]（空列表）

Django核心配置项！

当前Django项目中启用的app列表。 每个元素应该是一个Python的点分路径，字符串格式：

项目内每个启用的app，包括Django内置的contrib都必须在这个列表里注册，否则创建数据表、调用功能等等都无法进行。

一个典型的配置如下：
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app1',
    'app2',
]
```
建议在最后一个元素后面添加个逗号
28. LANGUAGE_CODE
默认值：'en-us'

当前项目所使用的语言。默认为英语。汉语是`zh-hans`，千万不要写成别的，比如‘chinese’之类！

USE_I18N必须设置为True才能使LANGUAGE_CODE生效。
29. LANGUAGES
默认值：所有可用语言的列表。

该配置项表示可用的语言种类，（语言代码，语言名称）两元组列表，例如：（'ja'， 'Japanese'）。

一般来说，默认值足够了。如果自定义LANGUAGES设置，可以使用ugettext_lazy()函数将语言名称标记为翻译字符串。

下面是一个示例设置文件：
```python
from django.utils.translation import ugettext_lazy as _

LANGUAGES = [
    ('de', _('German')),
    ('en', _('English')),
]
```
30. LOCALE_PATHS
默认值：[]（空列表）

Django查找翻译文件的目录列表,例如：
```python
LOCALE_PATHS = [
    '/home/www/project/common_files/locale',
    '/var/local/translations/locale',
]
```
Django将在这些路径中查找包含实际翻译文件的目录
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

警告：`MEDIA_ROOT`和`STATIC_ROOT`必须设置为不同的值。

34 . MEDIA_URL
默认值：''（空字符串）

`MEDIA_URL`指向`MEDIA_ROOT`所指定的media文件，用来管理保存的文件。该URL设置为非空值时，必须以斜杠“/”结束。

若你打算在模版中使用`{{ MEDIA_URL }}`，必须在`TEMPLATES`的`context_processors`设置中添加`django.template.context_processors.media`。

警告:`MEDIA_URL`和`STATIC_URL`必须设置为不同的值。

35 . MIDDLEWARE
默认值：None

Django1.10中的新功能。要使用的中间件列表。Django-admin命令创建的新项目中，`settings.py`文件里默认会为`MIDDLEWARE`配置项添加一系列Django内置的中间件，我们保持它不变就好了。

36 . ROOT_URLCONF
默认：未指定

一个字符串，表示根URLconf的完整Python导入路径。例如："mydjangoapps.urls"。

每个请求都可以覆盖它，通过设置HTTP请求HttpRequest对象的urlconf属性。但不是闲的，请不要特别设置，使用默认值就好。
37. SECRET_KEY
默认值：''（空字符串）

当前Django项目实例的密钥。用于提供cryptographic签名，是一个唯一的并且不可预测的值。比如：
```python
SECRET_KEY = '+$*n1_pkko%zaz3!lm8lg208@uj^qy3mcsuy+*ov%ikpvd5$rf'
```
通过`django-admin startproject xxx`命令创建的项目，会在`settings.py`中添加随机生成的`SECRET_KEY`。

如果未设置`SECRET_KEY`，Django将无法启动。

警告：不要透露该配置项的真实值！

38 . TEMPLATES
默认值：[]（空列表）

Django模板系统相关的配置。列表中每一项都是一个字典类型数据（类似DATABASE配置），可以配置模板不同的功能。

下面是一个例子：
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'APP_DIRS': True,
    },
]
```
以下选项适用于所有后端。

    + BACKEND：

默认：未指定

要使用的模板后端。 内置模板后端有：
```python
'django.template.backends.django.DjangoTemplates'
'django.template.backends.jinja2.Jinja2'
```
通过将`BACKEND`设置为完全限定路径（即'mypackage.whatever.Backend'），你可以使用第三方提供的模板后端。

    + NAME：

默认值：见下面

此特定模板引擎的别名，一个标识符，用于在某些情况下指定选择引擎进行渲染。别名在所有已配置的模板引擎中必须是唯一的。

当没有提供时，如果后端是'mypackage.whatever.Backend'，则其默认名称为'whatever'。

    + DIRS：

默认值：[]（空列表）

搜索模版的路径列表。搜索引擎会按照列表的排列顺序查找`template`资源文件。短路算法，找到即退出，不再往下找。

    + APP_DIRS

默认值：False

`Templates`搜索引擎是否应该在已安装的app中查找`Template`源文件。建议保持打开，即设置为True！

由django-admin startproject xxx命令创建的Django项目，其`settings.py`文件中`'APP_DIRS'`已经设置为True了。

    + OPTIONS：

默认值：{}

传递给模板后端的额外参数。 可用参数因模板后端而异。

39 . TIME_ZONE
默认：'America/Chicago'

时区设置。

注意，这个配置项的值不一定要和服务器的时区一致。例如，一个服务器可上可能有多个Django站点，每个站点都有一个单独的时区设置。

如果要设为中国时间，也就是北京时间，请赋值：`TIME_ZONE = 'Asia/Shanghai'`。注意是上海，不是北京，囧！

当`USE_TZ`为False时，它将成为Django存储所有日期和时间数据时,不使用时区。 当USE_TZ为True 时，它是Django显示模板中的时间，解释表单中的日期，使用时区。所以，通常我们都将`USE_TZ`同时设置为Ture！

注：在Windows 环境中，Django不能可靠地交替其它时区。如果你在Windows上运行Django，`TIME_ZONE`必须设置为与系统时区一致。

40 . USE_I18N
默认值：True

这是一个布尔值，指定Django的翻译系统是否开启。如果设置为False，Django会做一些优化，不去加载翻译机制。

由`django-admin startproject xxx`命令创建的Django项目，其`settings.py`文件中`'USE_I18N'`已经设置为True了。

41 . USE_L10N
默认值：False

一个布尔值，用于决定是否开启数据本地化。如果此设置为True，例如Django将使用当前语言环境的格式显示数字和日期。

由`django-admin startproject xxx`命令创建的Django项目，其`settings.py`文件中`'USE_L10N'`已经设置为True了。

42 . USE_TZ
默认值：False

一个布尔值,用来指定是否使用指定的时区(TIME_ZONE)的时间。若为True, 则Django会使用内建的时区的时间；否则, Django将会使用本地的时间。

由`django-admin startproject xxx`命令创建的Django项目，其`settings.py`文件中`'USE_TZ'`已经设置为True了。
43. WSGI_APPLICATION
默认值：None

Django的内置服务器（例如runserver）将使用的WSGI应用程序对象的完整Python路径。

Django使用WSGI协议与外部进行通信。

由`django-admin startproject xxx`命令创建的Django项目，将自动创建一个简单的`wsgi.py`模块，里面有一个可调用的`application`变量，`WSGI_APPLICATION`配置项的值就指向这个`application`变量。

下面是wsgi.py的源码：
```python
"""
WSGI config for mysite project.
It exposes the WSGI callable as a module-level variable named ``application``.
For more information on this file, see https://docs.djangoproject.com/en/1.11/howto/deployment/wsgi/
"""

import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings")

application = get_wsgi_application()
```
以下是一些子系统或工具框架的相关配置
Herry_DLZENG注销
目 录
Django教程
Django简介
Django环境安装
第一个Django应用
Part 1：请求与响应
Part 2：模型与管理后台
Part 3：视图和模板
Part 4：表单和类视图
Part 5：测试
Part 6：静态文件
Part 7：自定义admin站点
第一章：模型层model layer
模型和字段
关系类型字段
字段的参数
多对多中间表详解
模型的元数据Meta
模型的继承
用包来组织模型
查询操作
查询集API
不返回QuerySets的API
字段查询参数及聚合函数
第二章：视图层view layer
Django2.0 URL配置
URL路由基础
路由转发
URL反向解析和命名空间
视图函数及快捷方式
HttpRequest对象
QueryDict对象
HttpResponse对象
文件上传
动态生成CSV文件
动态生成PDF文件
第三章：模版层Template layer
Django模板语言详解
Django内置模板标签
Django内置模版过滤器
特殊的标签和过滤器
人类可读性
自定义模板标签和过滤器
第四章：Django表单
使用表单
Django表单API详解
Django表单字段汇总
表单的Widgets
模型表单ModelForm
第五章：Admin管理后台
自定制Admin
自定义Admin actions
Admin文档生成器
第六章：Django 综合篇
配置 Django
核心配置项
使用MySQL数据库
django-admin和manage.py
自定义django-admin命令
会话session
网站地图sitemap
信号 signal
序列化 serializers
消息框架 message
分页 Paginator
聚合内容 RSS/Atom
发送邮件
Django 日志
Django与缓存
认证系统 Authentication
Django与CSRF 、AJAX
Django 国际化和本地化
部署 Django
实战一：用户登录与注册系统
1. 搭建项目环境
2. 设计数据模型
3. admin后台
4. url路由和视图
5. 前端页面设计
6. 登录视图
7. Django表单
8. 图片验证码
9. session会话
10. 注册视图
11.使用Django发送邮件
12. 邮件注册确认
13. 使用Github管理项目
实战二：CMDB之资产管理系统
1.项目需求分析
2.模型设计
3.数据收集客户端
4.Windows下收集数据
5.Linux下收集数据
6.新资产待审批区
7.审批新资产
8.已上线资产信息更新
9.前端框架AdminLTE
10.资产总表
11.资产详细页面
12.dashboard仪表盘
核心配置项
阅读: 4781

Django的默认配置文件中，包含上百条配置项目，其中很多是我们‘一辈子’都不碰到或者不需要单独配置的，这些项目在需要的时候再去查手册。

强调：配置的默认值不是在settings.py文件中！不要以为settings.py中的配置值就是默认值，参考前文。settings.py是使用django-admin startproject xxx命令时，额外给我们创建的。

下面介绍的是61个相对比较常用和重要的配置项，按字母顺序排序，但是最后部分是cache、auth、message、session、static等的配置。

ADMINS
ALLOWED_HOSTS
APPEND_SLASH
DATABASES
DATE_FORMAT
DATE_INPUT_FORMATS
DATETIME_FORMAT
DATETIME_INPUT_FORMATS
DEBUG
DEFAULT_CHARSET
DEFAULT_CONTENT_TYPE
DEFAULT_FROM_EMAIL
DISALLOWED_USER_AGENTS
EMAIL_BACKEND
EMAIL_FILE_PATH
EMAIL_HOST
EMAIL_HOST_PASSWORD
EMAIL_HOST_USER
EMAIL_PORT
EMAIL_SUBJECT_PREFIX
EMAIL_USE_TLS
EMAIL_USE_SSL
EMAIL_SSL_CERTFILE
EMAIL_SSL_KEYFILE
EMAIL_TIMEOUT
FILE_CHARSET
INSTALLED_APPS
LANGUAGE_CODE
LANGUAGES
LOCALE_PATHS
LOGGING
LOGGING_CONFIG
MEDIA_ROOT
MEDIA_URL
MIDDLEWARE
ROOT_URLCONF
SECRET_KEY
TEMPLATES
TIME_ZONE
USE_I18N
USE_L10N
USE_TZ
WSGI_APPLICATION
CACHES
AUTHENTICATION_BACKENDS
AUTH_USER_MODEL
LOGIN_REDIRECT_URL
LOGIN_URL
LOGOUT_REDIRECT_URL
PASSWORD_RESET_TIMEOUT_DAYS
PASSWORD_HASHERS
MESSAGE_LEVEL
MESSAGE_STORAGE
SESSION_COOKIE_AGE
SESSION_COOKIE_NAME
SESSION_ENGINE
SESSION_EXPIRE_AT_BROWSER_CLOSE
SITE_ID
STATIC_ROOT
STATIC_URL
STATICFILES_DIRS
1. ADMINS
默认值：[]（空列表）

所有获得代码错误通知的人的邮件地址列表。当DEBUG=False，并且一个视图引发了异常时,Django将会给这个列表里的人发一封含有完整异常信息的电子邮件。列表中的每个项目都应该是（全名，电子邮件地址）的元组。例如：

[('John', 'john@example.com'), ('Mary', 'mary@example.com')]
2. ALLOWED_HOSTS
默认值：[]（空列表）

这是新手比较困惑的一个配置项。该配置项列表中包含的是Django站点可以为之提供服务的主机/域名。 也就是哪些主机或IP能够访问Django服务器！列表里的所有元素是共同存在的关系，不纯在冲突、优先级和排斥的关系。

列表中的值可以是localhost、www.example.com或者.example.com形式的域名。

也可以是IP地址，比如：137.2.4.1、192.168.1.1、0.0.0.0、127.0.0.1

还可以是通配符'*'，表示所有外部主机都可以访问Django。但这种情况具有安全风险，在线上环境不要使用。

对于0.0.0.0，表示局域网内的主机都可以访问Django。

当DEBUG为True和ALLOWED_HOSTS为空时，默认相当于配置：['localhost'， '127.0.0.1'， '[:: 1]']。

3. APPEND_SLASH
默认值：True

当设定为True时，如果请求的URL没有匹配到URLconf里面的任何一条URL路由设置，并且没有以/（斜杠）结束，该请求将重定向到以请求URL加/的URL地址。需要注意的是重定向有可能导致POST提交的数据丢失。

通俗的解释就是，如果你在写url时忘记了在最后添加一个斜杠，Django会默认帮你加上！请尽量保持默认值！

APPEND_SLASH设置只有在安装了CommonMiddleware中间件时才会启用。

4. DATABASES
默认值: {} (空的字典)

该配置项包含Django项目使用的所有数据库的设置。这是一个嵌套字典。

DATABASES设置必须配置一个default数据库，以及指定任意数量的其它数据库（可以没有）。

最简单的配置方式是使用SQLite数据库，Python和Django内置对SQLite数据库的支持，无需任何额外的安装和插件，如下所示：

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'mydatabase',
        # 或者'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
由于SQLite数据库就在Django项目本地，通常还是在Django项目根目录下，以一个文件的形式存在，没有用户名、密码、IP、port的问题，所以配置比较简单。

当连接其他数据库后端，比如MySQL、Oracle 或PostgreSQL，必须提供更多的连接参数。下面的例子用于PostgreSQL：

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
下面是DATABASES的内部配置项：

ATOMIC_REQUESTS
默认值：False

原子性事务请求。

AUTOCOMMIT
默认值：True

自动提交。

ENGINE
默认值：''（空字符串）

最重要的配置项！指定使用的数据库后端。 内建的数据库后端名称有：

'django.db.backends.postgresql'
'django.db.backends.mysql'
'django.db.backends.sqlite3'
'django.db.backends.oracle'
HOST
默认值：''（空字符串）

数据库所在的主机。设置的值可以是主机名、IP地址、和socket路径。空字符串表示localhost。SQLite不需要配置这个选项。

如果其值以斜杠（'/'）开头并且你使用的是MySQL，MySQL将通过Unix socket连接。 像这样：

"HOST": '/var/run/mysql'
NAME
默认值：''（空字符串）

使用的数据库名称。 对于SQLite，它是数据库文件的完整路径。指定路径时，请始终使用前向的斜杠，即使在Windows 上（例如C:/homes/user/mysite/sqlite3.db）。

但是对于MySQL等数据库，NAME指的是数据库系统中的具体某个database，Django没有能力自动在MySQL中创建数据库，它只能通过模型创建数据表。所以需要你通过各种数据客户端，在MySQL中提前创建好Django项目需要的数据库，使用命令CREATE DATABASE mysite CHARACTER SET utf8;，mysite是你的数据库名，务必同时指定字符编码方式为utf8！

CONN_MAX_AGE
默认：0

数据库连接的存活时间，以秒为单位。0表示在每个请求结束时关闭数据库连接，None表示无限的持久连接。

OPTIONS
Default: {} (默认为空的字典)

连接数据库时使用的额外参数。可用的参数与你的数据库后端有关。

PASSWORD
默认值：''（空字符串）

连接数据库时使用的密码。SQLite不需要这个选项。

PORT
默认值：''（空字符串）

连接数据库时使用的端口。空字符串表示默认的端口。SQLite不需要这个选项。 MySQL一般是3306。

TIME_ZONE
默认值：None

数据库中使用的时区。

USER
默认值：''（空字符串）

连接数据库时使用的用户名。SQLite不需要这个选项。

TEST
Default: {} (默认为空的字典)

测试数据库用的配置。

以下是测试数据库配置的示例：

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'USER': 'mydatabaseuser',
        'NAME': 'mydatabase',
        'TEST': {
            'NAME': 'mytestdatabase',
        },
    },
}
5. DATE_FORMAT
默认值：'N j, Y' (例如：Feb. 4, 2003)

系统中日期字段的默认显示格式。

6. DATE_INPUT_FORMATS
默认值：

[
    '%Y-%m-%d', '%m/%d/%Y', '%m/%d/%y', # '2006-10-25', '10/25/2006', '10/25/06'
    '%b %d %Y', '%b %d, %Y',            # 'Oct 25 2006', 'Oct 25, 2006'
    '%d %b %Y', '%d %b, %Y',            # '25 Oct 2006', '25 Oct, 2006'
    '%B %d %Y', '%B %d, %Y',            # 'October 25 2006', 'October 25, 2006'
    '%d %B %Y', '%d %B, %Y',            # '25 October 2006', '25 October, 2006'
]
日期字段中输入数据时将能够被接受的格式列表。格式将按顺序尝试，使用第一个有效的格式。

7. DATETIME_FORMAT
默认值：'N j, Y, P' (例如Feb. 4, 2003, 4 p.m.)

系统中显示datetime字段的默认格式。

8. DATETIME_INPUT_FORMATS
默认值：

[
    '%Y-%m-%d %H:%M:%S',     # '2006-10-25 14:30:59'
    '%Y-%m-%d %H:%M:%S.%f',  # '2006-10-25 14:30:59.000200'
    '%Y-%m-%d %H:%M',        # '2006-10-25 14:30'
    '%Y-%m-%d',              # '2006-10-25'
    '%m/%d/%Y %H:%M:%S',     # '10/25/2006 14:30:59'
    '%m/%d/%Y %H:%M:%S.%f',  # '10/25/2006 14:30:59.000200'
    '%m/%d/%Y %H:%M',        # '10/25/2006 14:30'
    '%m/%d/%Y',              # '10/25/2006'
    '%m/%d/%y %H:%M:%S',     # '10/25/06 14:30:59'
    '%m/%d/%y %H:%M:%S.%f',  # '10/25/06 14:30:59.000200'
    '%m/%d/%y %H:%M',        # '10/25/06 14:30'
    '%m/%d/%y',              # '10/25/06'
]
在datetime字段中输入数据时将被接受的格式列表。格式将按顺序尝试，使用第一个有效的格式。

9. DEBUG
默认值：False

打开/关闭调试模式。最重要的设置之一！默认值是False，你没有看错！只是在settings.py中又帮我们设置为True了，打开了调试模式，方便开发者和测试者的！线上部署网站的时候务必设置为False。

调试模式下可以显示错误页面的细节。若你的应用产生了一个异常，Django会显示追溯细节，包括许多环境变量的元数据, 比如所有当前定义的Django设置。

作为安全考虑，调试信息中不会列出包含下列关键字的配置项的内容，例如SECRET_KEY：

'API'
'KEY'
'PASS'
'SECRET'
'SIGNATURE'
'TOKEN'
注意，这里使用的是包含匹配，也就是说只要出现这些子字符串的配置项将匹配上。例如'PASS'能够匹配 PASSWORD, 'TOKEN'也将匹配TOKENIZED等等.

最后再次强调，如果DEBUG为False，你还需要正确设置ALLOWED_HOSTS以及静态文件。错误设置将导致对所有的请求返回“Bad Request (400)”。

10. DEFAULT_CHARSET
默认值：'utf-8'

HttpResponse响应对象的默认字符集。

11. DEFAULT_CONTENT_TYPE
默认值：'text/html'

HttpResponse对象的默认内容类型。

12. DEFAULT_FROM_EMAIL
默认值：'webmaster@localhost'

默认的电子邮件发送地址，即发送方。

13. DISALLOWED_USER_AGENTS
默认值：[]（空列表）

这是一个编译好了的正则表达式对象的列表。代表哪些不允许访问任何页面的User-Agent字符串。也就是说如果一个请求的User-Agent属性，被这个配置项中的任何一个正则表达式匹配到了，那么这个请求将被阻止。常用于对付机器人和网络蜘蛛。需要CommonMiddleware中间件支持。



