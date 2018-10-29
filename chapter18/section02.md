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