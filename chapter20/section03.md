14 . EMAIL_BACKEND
默认值：' django.core.mail.backends.smtp.EmailBackend '

用于发送邮件的后端。

15 . EMAIL_FILE_PATH
默认：未指定

邮件后端保存输出文件时使用的目录。

16  .  EMAIL_HOST
默认：'localhost'

发送邮件使用的主机。

17 . EMAIL_HOST_PASSWORD
默认值：''（空字符串）

EMAIL_HOST的SMTP服务器使用的密码。

18 . EMAIL_HOST_USER
默认值：''（空字符串）

EMAIL_HOST的SMTP服务器使用的用户名。

19 . EMAIL_PORT
默认：25

EMAIL_HOST的SMTP服务器使用的端口。

20 . EMAIL_SUBJECT_PREFIX
默认值：'[Django] '

使用django.core.mail.mail_admins或django.core.mail.mail_managers发送的电子邮件的主题行前缀。

21 . EMAIL_USE_TLS
默认值：False

是否使用TLS(安全)与SMTP服务器连接。用于显式TLS连接,通常在端口587上。

22 . EMAIL_USE_SSL
默认值：False

在与SMTP服务器通信时是否使用隐式TLS（安全）连接。在大多数电子邮件文档中，此类型的TLS连接称为SSL。 它通常在端口465上使用。

注意：腾讯家的qq邮箱服务，需要使用ssl安全链接在465端口上！

请注意，EMAIL_USE_TLS与EMAIL_USE_SSL是互斥的，因此只能将其中一个设置设置为True。

23 . EMAIL_SSL_CERTFILE
默认值：None

如果EMAIL_USE_SSL或EMAIL_USE_TLS为True，则可以选择指定要用于SSL连接的PEM格式的证书链文件的路径。

24 . EMAIL_SSL_KEYFILE
默认值：None

如果EMAIL_USE_SSL或EMAIL_USE_TLS为True，可以选择指定要用于SSL连接的PEM格式的私钥文件的路径。

25 . EMAIL_TIMEOUT
默认值：None

邮件发送超时时间。

26 . FILE_CHARSET
默认值：'utf-8'

从磁盘读取文件时使用的字符编码。包括模板文件和初始SQL数据文件。

没有特别需求，请不要修改它。

27 . INSTALLED_APPS
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
建议在最后一个元素后面添加个逗号。

28 . LANGUAGE_CODE
默认值：'en-us'

当前项目所使用的语言。默认为英语。汉语是zh-hans，千万不要写成别的，比如‘chinese’之类！

USE_I18N必须设置为True才能使LANGUAGE_CODE生效。

29 . LANGUAGES
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
Django将在这些路径中查找包含实际翻译文件的目录。

31 . LOGGING
默认值：日志配置字典。

日志配置信息。

32 . LOGGING_CONFIG
默认值：'logging.config.dictConfig'

用于在Django项目中配置日志记录的可调用项的路径。

33 . MEDIA_ROOT
默认值：''（空字符串）

用户上传的文件，所在目录的，文件系统绝对路径。也就是指示上传文件放到哪里。

例如： "/var/www/example.com/media/"

警告：MEDIA_ROOT和STATIC_ROOT必须设置为不同的值。

34 . MEDIA_URL
默认值：''（空字符串）

MEDIA_URL指向MEDIA_ROOT所指定的media文件，用来管理保存的文件。该URL设置为非空值时，必须以斜杠“/”结束。

若你打算在模版中使用{{ MEDIA_URL }}，必须在TEMPLATES的context_processors设置中添加django.template.context_processors.media。

警告:MEDIA_URL和STATIC_URL必须设置为不同的值。

35 . MIDDLEWARE
默认值：None

Django1.10中的新功能。要使用的中间件列表。Django-admin命令创建的新项目中，settings.py文件里默认会为MIDDLEWARE配置项添加一系列Django内置的中间件，我们保持它不变就好了。

36 . ROOT_URLCONF
默认：未指定

一个字符串，表示根URLconf的完整Python导入路径。例如："mydjangoapps.urls"。

每个请求都可以覆盖它，通过设置HTTP请求HttpRequest对象的urlconf属性。但不是闲的，请不要特别设置，使用默认值就好。

37 . SECRET_KEY
默认值：''（空字符串）

当前Django项目实例的密钥。用于提供cryptographic签名，是一个唯一的并且不可预测的值。比如：

SECRET_KEY = '+$*n1_pkko%zaz3!lm8lg208@uj^qy3mcsuy+*ov%ikpvd5$rf'
通过django-admin startproject xxx命令创建的项目，会在settings.py中添加随机生成的SECRET_KEY。

如果未设置SECRET_KEY，Django将无法启动。

警告：不要透露该配置项的真实值！

38 . TEMPLATES
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

    + BACKEND：

默认：未指定

要使用的模板后端。 内置模板后端有：

'django.template.backends.django.DjangoTemplates'
'django.template.backends.jinja2.Jinja2'
通过将BACKEND设置为完全限定路径（即'mypackage.whatever.Backend'），你可以使用第三方提供的模板后端。

    + NAME：

默认值：见下面

此特定模板引擎的别名，一个标识符，用于在某些情况下指定选择引擎进行渲染。别名在所有已配置的模板引擎中必须是唯一的。

当没有提供时，如果后端是'mypackage.whatever.Backend'，则其默认名称为'whatever'。

    + DIRS：

默认值：[]（空列表）

搜索模版的路径列表。搜索引擎会按照列表的排列顺序查找template资源文件。短路算法，找到即退出，不再往下找。

    + APP_DIRS

默认值：False

Templates搜索引擎是否应该在已安装的app中查找Template源文件。建议保持打开，即设置为True！

由django-admin startproject xxx命令创建的Django项目，其settings.py文件中'APP_DIRS'已经设置为True了。

    + OPTIONS：

默认值：{}

传递给模板后端的额外参数。 可用参数因模板后端而异。


