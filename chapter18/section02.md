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