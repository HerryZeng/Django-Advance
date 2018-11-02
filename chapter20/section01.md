## 配置 Django

Django项目的设置文件位于项目同名目录下，名叫`settings.py`。这个模块，集合了整个项目方方面面的设置属性，是项目启动和提供服务的根本保证。

### 一、简述
`settings.py`文件本质上是一个Python模块，带有模块级别的变量。

下面是一些示例设置：
```python
ALLOWED_HOSTS = ['www.example.com']
DEBUG = False
DEFAULT_FROM_EMAIL = 'webmaster@example.com'
```
注：当`DEBUG`为`False`时，必须设置`ALLOWED_HOSTS`的值。

配置settings.py时：
    + 不允许出现Python层面的语法错误；
    + 可以使用普通的Python语法动态地设置，例如：`MY_SETTING = [str(i) for i in range(30)]`；
    + 可以从其它设置文件导入值。
    
    
### 二、指定配置文件

当你使用Django时，你必须告诉它你要使用哪个配置文件来启动服务。也就是给环境变量`DJANGO_SETTINGS_MODULE`赋值。这个值要使用Python路径的语法，例如`mysite.settings`，而不是操作系统的文件路径语法。 注意，被设置的配置文件应该在Python的导入查找路径中。

默认情况下，我们是不需要设置这个变量的，直接启动项目就可以。但是，有时候就会有需要使用别的配置启动项目的情形。

django-admin命令：

当使用django-admin命令时， 可以设置临时环境变量，或者每次运行该工具时显式地指定配置文件。

例如在Unix Bash shell下：
```bash
export DJANGO_SETTINGS_MODULE=mysite.settings
django-admin runserver
```
在Windows shell下，也就是cmd环境：
```bash
set DJANGO_SETTINGS_MODULE=mysite.settings
django-admin runserver
```
使用`--settings`命令行参数可以手工指定：
```bash
django-admin runserver --settings=mysite.settings
```
如果是在服务器环境中，比如mod_wsgi网关接口，需要告诉WSGI，你准备使用哪个设置文件。这可以使用os.environ实现：
```python
import os

os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'
```
再次强调，所有上面的操作都要求`mysite.settings`必须在查找路径中，否则要用绝对路径。

### 三、默认配置

Django的配置文件并不需要定义所有的选项，每个选项都有一个默认值，这些默认值位于`django/conf/global_settings.py`模块中。

Django加载配置的顺序是这样的：
    1. 从global_settings.py中加载默认配置；
    2. 从指定的配置文件中加载（通常是settings.py），如有必要则覆盖global_settings.py中的默认配置。
有一个简单的方法可以查看当前有哪些设置与默认的设置不一样了，也就是`python manage.py diffsettings`命令。

### 四、在Django环境中使用settings

所谓的在Django环境中，指的是要使用`settings`的模块（可以简单的理解为局域网内主机），必须是Django工作状态中能够链接的模块，不能是孤零零，额外的一个Python脚本（外部主机）。这时，可以通过导入`django.conf.settings`来使用配置文件。 例如：
```python
from django.conf import settings

if settings.DEBUG:
    # Do something
```
注意，django.conf.settings不是一个模块，而是一个对象。 所以不可以单独导入每个配置项：
```python
from django.conf.settings import DEBUG  # 这是错误的做法
```

### 五、不要在运行时更改设置

请不要在Django项目运行时改变设置。 例如，不要在视图中这样做：
```python
from django.conf import settings

settings.DEBUG = True   # 不要这么做
```

### 六、注意安全
因为`settings.py`经常会包含敏感的信息，例如管理员、远程主机、数据库的用户名或密码，你应该尽一切可能来限制对它的访问。 例如，修改它的文件权限使得只有你和Web服务器使用者可以读取它。


### 七、添加自己的配置项
如果要添加自己的配置项，需遵循以下准则：
    + 配置项名称必须全为大写。
    + 不要使用一个已经存在的设置
    
### 八、自定义默认设置
如果你想让默认值来自其它地方而不是`django.conf.global_settings`，你可以传递一个提供默认设置的模块或类作为`default_settings`参数（或第一个位置参数）给configure()方法调用。

在下面的示例中，默认的设置来自`myapp_defaults`，并且单独设置`DEBUG`为`True`，而不论它在`myapp_defaults`中的值是什么：
```python
from django.conf import settings
from myapp import myapp_defaults
settings.configure(default_settings=myapp_defaults, DEBUG=True)
```
下面的示例和上面一样，只是使用`myapp_defaults`作为一个位置参数：
```python
settings.configure(myapp_defaults, DEBUG=True)
```
正常情况下，还是不要用这种方式覆盖默认值。Django的默认配置文件还是很可靠的，你可以安全地使用它们。 注意，如果你使用自己写的默认模块，它将完全取代Django的默认模块，你必须指定每个可能用到的配置项的值。 完整的配置项清单，参考`django.conf.settings.global_settings`模块。


### 外部脚本调用Django环境：django.setup()

如果你使用外部脚本， 加载一些Django模板，或者使用ORM来获取一些数据，除了配置`settings`模块之外，还需要一个步骤。

也就是在设置`DJANGO_SETTINGS_MODULE`或调用`configure()`之后，还需要调用`django.setup()`，像这样：
```python
import django
from django.conf import settings
from myapp import myapp_defaults

settings.configure(default_settings=myapp_defaults, DEBUG=True)
django.setup()

# 现在可以访问Django项目内部的模块了
from myapp import models
```
请注意，只有真正独立的外部脚本，才需要调用`django.setup()`。前面有说到过，当处于服务器调用环境，或通过`django-admin`调用，Django将自动为你加载环境。

`django.setup()`只能调用一次。尽量使用下面的方式，防止重复调用：
```python
if __name__ == '__main__':
    import django
    django.setup()
```