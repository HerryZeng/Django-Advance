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

