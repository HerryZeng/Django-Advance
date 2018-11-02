## 核心配置项

Django的默认配置文件中，包含上百条配置项目，其中很多是我们‘一辈子’都不碰到或者不需要单独配置的，这些项目在需要的时候再去查手册。

强调：配置的默认值不是在`settings.py`文件中！不要以为`settings.py`中的配置值就是默认值，参考前文。`settings.py`是使用`django-admin startproject xxx`命令时，额外给我们创建的。

下面介绍的是61个相对比较常用和重要的配置项，按字母顺序排序，但是最后部分是`cache、auth、message、session、static`等的配置。

|配置项|说明|
|---|---|
|ADMINS|代码错误通知的人的邮件地址列表|
|ALLOWED_HOSTS|该配置项列表中包含的是Django站点可以为之提供服务的主机/域名|
|APPEND_SLASH|请求重定向到以请求URL加/的URL地址|
|DATABASES||
|DATE_FORMAT||
|DATE_INPUT_FORMATS||
|DATETIME_FORMAT||
|DATETIME_INPUT_FORMATS||
|DEBUG||
|DEFAULT_CHARSET||
|DEFAULT_CONTENT_TYPE||
|DEFAULT_FROM_EMAIL||
|DISALLOWED_USER_AGENTS||
|EMAIL_BACKEND||
|EMAIL_FILE_PATH|
|EMAIL_HOST||
|EMAIL_HOST_PASSWORD||
|EMAIL_HOST_USER||
|EMAIL_PORT||
|EMAIL_SUBJECT_PREFIX||
|EMAIL_USE_TLS||
|EMAIL_USE_SSL||
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
