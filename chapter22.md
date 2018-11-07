## 消息框架 message

在网页应用中，我们经常需要在处理完表单或其它类型的用户输入后，显示一个通知信息给用户。

对于这个需求，Django提供了基于`Cookie`或者会话的消息框架`messages`，无论是匿名用户还是认证的用户。这个消息框架允许你临时将消息存储在请求中，并在接下来的请求（通常就是下一个请求）中提取它们并显示。每个消息都带有一个特定的`level`标签，表示其优先级（例如`info`、 `warning`或`error`）。

---

### 一、启用消息框架

Django的`messages`消息框架的实现，依赖`messages`中间件和对应的`context processor`。

通过`django-admin startproject xxx`命令创建工程时，已经默认在`settings.py`中开启了消息框架功能需要的所有的设置：`INSTALLED_APPS`中注册的`'django.contrib.messages'`。

`MIDDLEWARE`中添加`'django.contrib.sessions.middleware.SessionMiddleware'`和`'django.contrib.messages.middleware.MessageMiddleware'`。Django的`messages`框架默认使用的存储后端为`sessions`。所以`Session`中间件必须被启用，并出现在`Message`中间件之前。

`TEMPLATES`设置中的`DjangoTemplates`选项包含的`'context_processors'`配置项要包含`'django.contrib.messages.context_processors.messages'`。


---

### 二、配置消息引擎

通常我们使用默认的就好，可以跳过这节，但如果真有需要，也可以配置：

1. 存储后端
Django提供了三种内置的消息存储后端：
    + storage.session.SessionStorage
    + storage.cookie.CookieStorage
    + storage.fallback.FallbackStorage
    
`FallbackStorage`是默认的存储后端。如果它不适合你的需要，你可以通过设置`MESSAGE_STORAGE`选择另外一个存储后端，例如：
```python
MESSAGE_STORAGE = 'django.contrib.messages.storage.cookie.CookieStorage'
```

2. 消息级别
消息框架的级别是可配置的，与Python的logging模块类似

Django内置的message级别有下面几种：

|级别|说明|
|--- | ---|
|DEBUG|	将在生产部署中忽略（或删除）的与开发相关的消息|
|INFO|	普通提示信息|
|SUCCESS|	成功信息|
|WARNING|	警告信息|
|ERROR|	已经发生的错误信息|

`MESSAGE_LEVEL`设置可以用来改变记录的最小级别（参考前面的Django设置章节），小于这个级别的消息将被忽略。

3. 消息样式
通常，我们在前端HTML页面中，希望给不同级别的消息，增加不同的CSS样式，比如警告为黄色，error为红色等等。

Django为我们提供了一个默认的样式对应关系：

|级别|	样式|
|---|---|
|DEBUG|	debug|
|INFO|	info|
|SUCCESS|	success|
|WARNING|	warning|
|ERROR|	error|

也就是说SUCCESS级别的消息，在前端会被赋予一个success样式class。

若要修改消息级别的默认样式，设置MESSAGE_TAGS，按如下例子所示：。
```python
from django.contrib.messages import constants as messages
MESSAGE_TAGS = {
    messages.INFO: '',
    50: 'critical',
}
```

---

### 三、使用消息框架

1. 添加消息
方法原型：`add_message(request, level, message, extra_tags='', fail_silently=False)`

新增一条消息：
```python
from django.contrib import messages
messages.add_message(request, messages.INFO, 'Hello world.')
```
提供请求对象`request`（直接用就行），消息级别、消息内容字符串三个参数即可。

或者使用下面的快捷方式
```python
messages.debug(request, '%s SQL statements were executed.' % count)
messages.info(request, 'Three credits remain in your account.')
messages.success(request, 'Profile details updated.')
messages.warning(request, 'Your account expires in three days.')
messages.error(request, 'Document deleted.')
```

2. 显示消息
方法原型：`get_messages(request)`

在你的模板文件中，像下面这样使用：
```html
{% if messages %}
<ul class="messages">
    {% for message in messages %}
    <li{% if message.tags %} class="{{ message.tags }}"{% endif %}>{{ message }}</li>
    {% endfor %}
</ul>
{% endif %}
```
相关说明：
    + 通过if判断是否有消息；
    + `messages`是一个列表，必须用for标签循环它；
    + 即使你知道只有一条消息，也要迭代`messages`列表，否则下个请求中，上个请求的消息不会被清除。
    + 可以通过`message.tags`拿到每个消息的CSS样式
    
有一个DEFAULT_MESSAGE_LEVELS变量，它映射消息级别的名称到它们的数值：
```html
{% if messages %}
<ul class="messages">
    {% for message in messages %}
    <li{% if message.tags %} class="{{ message.tags }}"{% endif %}>
        {% if message.level == DEFAULT_MESSAGE_LEVELS.ERROR %}Important: {% endif %}
        {{ message }}
    </li>
    {% endfor %}
</ul>
{% endif %}
```
说明：
    + 可以通过`message.level`拿到当前消息的级别数值；
    + 将它与`DEFAULT_MESSAGE_LEVELS.ERROR`进行对比；
    + 如果一样，就说明当前消息级别为ERROR，需要显示到页面上。

在模板的外面，比如视图中，可以使用get_messages()方法获取消息：
```python
from django.contrib.messages import get_messages

storage = get_messages(request)
for message in storage:
    do_something_with_the_message(message)
```
说明：
    + `get_messages()`返回的是存储后端的一个实例。
    + 循环这个实例，可以获得每条消息
    
对于每一个消息实例，都包含下面的属性，可以在模版或视图中调用：
    + `message`: 消息的实际内容文本。不要使用`message.message`，直接`message`。
    + `level`: 消息级别，一个整数。
    + `tags`: 一个字符串，由该消息的所有标签(extra_tags和tags)组合而成，组合时用空格分割开这些标签。
    + `extra_tags`: 一个字符串，由该消息的定制标签组合而成，并用空格分割。默认为空。
    + `level_tag`: 当前消息级别对应的CSS字符串，前面介绍过。
    
3 . 自定义消息级别
消息级别只是一个整数常量，所以，可以定义自己的级别常量，例如：
```python
CRITICAL = 50

def my_view(request):
    messages.add_message(request, CRITICAL, 'A serious error occurred.')
```
在自定义消息级别时，应小心避免覆盖现有级别。内置级别的值为：

|级别|	对应整数值|
|--- |---|
|DEBUG|	10|
|INFO|	20|
|SUCCESS|	25|
|WARNING|	30|
|ERROR|	40|

如果你需要在HTML或CSS中使用自定义级别，则需要通过`MESSAGE_TAGS`设置提供相应的映射关系。

4 . 自定义每个请求的最小记录级别

每个请求都可以通过`set_level()`方法设置最小记录级别，如下所示:
```python
from django.contrib import messages

# 修改最小级别为DEBUG
messages.set_level(request, messages.DEBUG)
messages.debug(request, 'Test message...')

# 在另外一个视图中修改最小级别为WARNING
messages.set_level(request, messages.WARNING)
messages.success(request, 'Your profile was updated.') # 被忽略，不记录
messages.warning(request, 'Your account is about to expire.') # 记录

# 将最小级别恢复到默认值
messages.set_level(request, None)
```

`set_level()`方法接收`request`为第一参数，消息级别为第二参数。
类似的，当前有效的记录级别可以用get_level()方法获取:
```python
from django.contrib import messages
current_level = messages.get_level(request)
```

5 . 添加额外的消息CSS样式
要添加自定义的消息CSS样式，可以通过extra_tags参数：
```python
messages.add_message(request, messages.INFO, 'Over 9000!', extra_tags='dragonball')
messages.error(request, 'Email box full', extra_tags='email')
```

---

### 四、消息过期机制

默认情况下，如果包含消息的迭代器完成迭代后，当前请求中的消息都将被删除。

如果你不想这么做，想保留这些消息，那么需要显式的指定used参数为False，如下所示：
```python
storage = messages.get_messages(request)
for message in storage:
    do_something_with(message)
storage.used = False
```