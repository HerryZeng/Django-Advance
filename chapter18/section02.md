## Django 日志

`Django`使用`Python`内置的`logging`模块实现它自己的日志系统。如果你没有使用过`logging`模块，可以细节阅读上一节《[logging模块详解](section01.md)》。

在Python的logging模块中，主要包含下面四大金刚：
    + Loggers： 记录器
    + Handlers：处理器
    + Filters： 过滤器
    + Formatters： 格式化器

### 在Django视图中使用logging

使用方法非常简单，如下例所示：
```python
# 导入logging库
import logging

# 获取一个logger对象
logger = logging.getLogger(__name__)

def my_view(request, arg1, arg):
    ...
    if bad_mojo:
        # 记录一个错误日志
        logger.error('Something went wrong!')
```
每满足`bad_mojo`条件一次，就写入一条错误日志。

实际上，logger对象有下面几个常用内置方法：

    + logger.debug()
    + logger.info()
    + logger.warning()
    + logger.error()
    + logger.critical()
    
### 在Django中配置logging

通常，只是像上面的例子那样简单的使用logging模块是远远不够的，我们一般都要对logging的四大金刚进行一定的配置。`Python`的`logging`模块提供了好几种配置方式。默认情况下，`Django`使用`dictConfig format`。也就是字典方式。
例一：
```yaml
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': '/path/to/django/debug.log',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'DEBUG',
            'propagate': True,
        },
    },
}
```
如果你使用上面的样例，请确保`Django`用户对'`filename`'对应目录和文件的写入权限。

例二：

下面这个示例配置，让Django将日志打印到控制台，通常用做开发期间的信息展示。
```yaml
import os

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['console'],
            'level': os.getenv('DJANGO_LOG_LEVEL', 'INFO'),
        },
    },
}
```

例三：

下面是一个相当复杂的logging配置：
```yaml
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s %(process)d %(thread)d %(message)s'
        },
        'simple': {
            'format': '%(levelname)s %(message)s'
        },
    },
    'filters': {
        'special': {
            '()': 'project.logging.SpecialFilter',
            'foo': 'bar',
        },
        'require_debug_true': {
            '()': 'django.utils.log.RequireDebugTrue',
        },
    },
    'handlers': {
        'console': {
            'level': 'INFO',
            'filters': ['require_debug_true'],
            'class': 'logging.StreamHandler',
            'formatter': 'simple'
        },
        'mail_admins': {
            'level': 'ERROR',
            'class': 'django.utils.log.AdminEmailHandler',
            'filters': ['special']
        }
    },
    'loggers': {
        'django': {
            'handlers': ['console'],
            'propagate': True,
        },
        'django.request': {
            'handlers': ['mail_admins'],
            'level': 'ERROR',
            'propagate': False,
        },
        'myproject.custom': {
            'handlers': ['console', 'mail_admins'],
            'level': 'INFO',
            'filters': ['special']
        }
    }
}
```
上面的logging配置主要定义了这么几件事情：
    + 定义了配置文件的版本，当前版本号为1.0
    + 定义了两个formatter：simple和format，分别表示两种文本格式。
    + 定义了两个过滤器：SpecialFilter和RequireDebugTrue
    + 定义了两个处理器：console和mail_admins
    + 配置了三个logger：'django'、'django.request'和'myproject.custom'
    
### Django对logging模块的扩展

Django对logging模块进行了一定的扩展，用来满足Web服务器专门的日志记录需求。

1. 记录器 Loggers
Django额外提供了几个其内建的logger。
    + django： 不要使用这个记录器，用下面的。这是一个被供起来的记录器，^-^
    + django.request： 记录与处理请求相关的消息。5XX错误被记录为ERROR消息；4XX错误记录为WARNING消息。接收额外参数：status_code和request
    + django.server： 记录开发服务器下处理请求相关的消息。只用于开发阶段。
    + django.template: 记录与渲染模板相关的日志。
    + django.db.backends: 与数据库交互的代码相关的消息。
    + django.security： 记录任何与安全相关的错误。
    + django.security.csrf： 记录CSRF验证失败日志。
    + django.db.backends.schema： 记录查询导致数据库修改的日志。
    
2. 处理器 Handlers
Django额外提供了一个handler，AdminEmailHandler。这个处理器将它收到的每个日志信息用邮件发送给站点管理员。
3. 过滤器Filters
Django还额外提供两个过滤器。
    + CallbackFilter(callback)[source]：这个过滤器接受一个回调函数，并对每个传递给过滤器的记录调用它。如果回调函数返回False，将不会进行记录的处理。
    + RequireDebugFalse[source]： 这个过滤器只会在settings.DEBUG==False时传递。