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
|EMAIL_SSL_CERTFILE||
|EMAIL_SSL_KEYFILE||
|EMAIL_TIMEOUT||
|FILE_CHARSET||
|INSTALLED_APPS||
|LANGUAGE_CODE||
|LANGUAGES||
|LOCALE_PATHS||
|LOGGING||
|LOGGING_CONFIG||
|MEDIA_ROOT||
|MEDIA_URL||
|MIDDLEWARE||
|ROOT_URLCONF||
|SECRET_KEY||
|TEMPLATES||
|TIME_ZONE||
|USE_I18N||
|USE_L10N||
|USE_TZ||
|WSGI_APPLICATION||
|CACHES||
|AUTHENTICATION_BACKENDS||
|AUTH_USER_MODEL||
|LOGIN_REDIRECT_URL||
|LOGIN_URL||
|LOGOUT_REDIRECT_URL||
|PASSWORD_RESET_TIMEOUT_DAYS||
|PASSWORD_HASHERS||
|MESSAGE_LEVEL||
|MESSAGE_STORAGE||
|SESSION_COOKIE_AGE||
|SESSION_COOKIE_NAME||
|SESSION_ENGINE||
|SESSION_EXPIRE_AT_BROWSER_CLOSE||
|SITE_ID||
|STATIC_ROOT||
|STATIC_URL||
|STATICFILES_DIRS||

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
6. DATE_INPUT_FORMATS
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
7. DATETIME_FORMAT
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