14. EMAIL_BACKEND
默认值：' django.core.mail.backends.smtp.EmailBackend '

用于发送邮件的后端。

15. EMAIL_FILE_PATH
默认：未指定

邮件后端保存输出文件时使用的目录。

16. EMAIL_HOST
默认：'localhost'

发送邮件使用的主机。

17. EMAIL_HOST_PASSWORD
默认值：''（空字符串）

EMAIL_HOST的SMTP服务器使用的密码。

18. EMAIL_HOST_USER
默认值：''（空字符串）

EMAIL_HOST的SMTP服务器使用的用户名。

19. EMAIL_PORT
默认：25

EMAIL_HOST的SMTP服务器使用的端口。

20. EMAIL_SUBJECT_PREFIX
默认值：'[Django] '

使用django.core.mail.mail_admins或django.core.mail.mail_managers发送的电子邮件的主题行前缀。

21. EMAIL_USE_TLS
默认值：False

是否使用TLS(安全)与SMTP服务器连接。用于显式TLS连接,通常在端口587上。

22. EMAIL_USE_SSL
默认值：False

在与SMTP服务器通信时是否使用隐式TLS（安全）连接。在大多数电子邮件文档中，此类型的TLS连接称为SSL。 它通常在端口465上使用。

注意：腾讯家的qq邮箱服务，需要使用ssl安全链接在465端口上！

请注意，EMAIL_USE_TLS与EMAIL_USE_SSL是互斥的，因此只能将其中一个设置设置为True。

23. EMAIL_SSL_CERTFILE
默认值：None

如果EMAIL_USE_SSL或EMAIL_USE_TLS为True，则可以选择指定要用于SSL连接的PEM格式的证书链文件的路径。

24. EMAIL_SSL_KEYFILE
默认值：None

如果EMAIL_USE_SSL或EMAIL_USE_TLS为True，可以选择指定要用于SSL连接的PEM格式的私钥文件的路径。

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
建议在最后一个元素后面添加个逗号。

28. LANGUAGE_CODE
默认值：'en-us'

当前项目所使用的语言。默认为英语。汉语是zh-hans，千万不要写成别的，比如‘chinese’之类！

USE_I18N必须设置为True才能使LANGUAGE_CODE生效。

29. LANGUAGES
默认值：所有可用语言的列表。

该配置项表示可用的语言种类，（语言代码，语言名称）两元组列表，例如：（'ja'， 'Japanese'）。

一般来说，默认值足够了。如果自定义LANGUAGES设置，可以使用ugettext_lazy()函数将语言名称标记为翻译字符串。

下面是一个示例设置文件：

from django.utils.translation import ugettext_lazy as _

LANGUAGES = [
    ('de', _('German')),
    ('en', _('English')),
]
30. LOCALE_PATHS
默认值：[]（空列表）

Django查找翻译文件的目录列表,例如：

LOCALE_PATHS = [
    '/home/www/project/common_files/locale',
    '/var/local/translations/locale',
]
Django将在这些路径中查找包含实际翻译文件的目录。
