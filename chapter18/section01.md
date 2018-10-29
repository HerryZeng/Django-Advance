## 基础使用

### logging使用场景

日志是什么？这个不用多解释。百分之九十的程序都需要提供日志功能。`Python`内置的`logging`模块，为我们提供了现成的高效的日志解决方案。但是，不是所有的场景都需要使用`logging`模块。下面是`Python`官方推荐的使用方法：

|任务场景|最佳工具|
|---|---|
|普通情况下，在控制台显示输出|`print()`|
|报告正常程序操作过程中发生的事件|`logging.info()`或更详细的`logging.debug()`|
|发出有关特定事件的警告|`warnings.warn()`或`logging.warning()`|
|报告错误|弹出异常|
|在不引发异常的情况下报告错误|`logging.error()`,`logging.exception()`或`logging.critical()`|

`logging`模块定义了下表所示的日志级别，按事件严重程序由低到高排列（**注意是全部大写，因为它们是常量**）

|**级别**|**级别数值**|
|---|---|
|DEBUG|10|
|INFO|20|
|WARNING|30|
|ERROR|40|
|CRITICAL|50|

默认级别是`WARNING`，表示只有`WARNING`和比`WARNING`更严重的事件才会被记录到日志内，低级别的信息会被忽略。因此，默认情况下，`DEBUG`和`INFO`会被忽略，`WARNING`,`ERROR`和`CRITICAL`会被记录。

有多种方法来处理被跟踪的事件。最简单的方法是把它们打印到终端控制台上，或将它们写入一个磁盘文件内。

### 简单范例

在什么都不配置和设定的情况下，`logging`会简单地将日志打印在显示器上，如下例所示：
```python
    import logging
    logging.warning('Watch out!')      # 消息会被打印到控制台上
    logging.info('I told you so')      # 这行不会被打印，因为级别低于默认级别
```
如果，将上面的代码放在一个脚本里并运行，结果是：
```log
    WARNING:root:Watch out！
```
默认情况下，打印出来的内容包括日志级别，调用者和具体的日志信息。所有的这些内容都可以自定义的，在后面再细说。

### 记录到文件内

要把日志界内到文件内，就不能使用上面的方法了，但是`logging`模块同样给我们提供了一个相对便捷的方法。那就是`logging.basicConfig()`方法。示例如下：
```python
    import logging
    
    logging.basicConfig(filename='example.log',level=logging.DEBUG)
    logging.debug('This message should go to the log file')
    logging.info('So should this')
    logging.warning('And this,too')
```
然后打开`example.log`文件，可以看到下面的日志消息：
```log
    DEBUG:root:This message should go to the log file
    INFO:root:So should this
    WARNING:root:And this, too
```
我们通过`level=logging.DEBUG`参数，设定了日志记录的门槛。如果想在命令行调用时设置日志级别，可以使用下面的选项：
```python
    --log=INFO
```
可以通过下面的方法来获取用户输入的日志级别参数：
```python
    numeric_level = getattr(logging, loglevel.upper(), None)
    if not isinstance(numeric_level, int):
        raise ValueError('Invalid log level: %s' % loglevel)
    logging.basicConfig(level=numeric_level, ...)
```
默认情况下，日志会不断的追加到文件的后面。如果你不想保存之前的日志，每次都清空文件，然后写入当前日志，则可以如下设置：
```python
    logging.basicConfig(filename='example.log', filemode='w', level=logging.DEBUG)
```
关键是将`filemode`设置为'w'。

### 多模块中同时使用日志功能
如果你的程序包含多个文件（模块），下面是个如何在其中组织日志的例子：
```python
# myapp.py
import logging
import mylib

def main():
    logging.basicConfig(filename='myapp.log', level=logging.INFO)
    logging.info('Started')
    mylib.do_something()
    logging.info('Finished')

if __name__ == '__main__':
    main()
```
```python
# mylib.py
import logging

def do_something():
    logging.info('Doing something')
```
运行myapp.py模块，你可以在myapp.log日志文件中看到下面的内容：
```log
INFO:root:Started
INFO:root:Doing something
INFO:root:Finished
```

### 日志的变量数据

在logging模块中通过百分符%方式的格式化控制，生成消息字符串，类同于字符串数据类型的格式化输出，但也有不同之处。
```python
import logging
logging.warning('%s before you %s', 'Look', 'leap!')
```
结果
```log
WARNING:root:Look before you leap!
```
可以看到两个%s分别被‘Look’和‘leap！’替代了。

### 消息格式

要控制消息格式，获得更多的花样，可以提供format参数：
```python
import logging
logging.basicConfig(format='%(levelname)s:%(message)s', level=logging.DEBUG)
logging.debug('This message should appear on the console')
logging.info('So should this')
logging.warning('And this, too')
```
输出结果
```log
DEBUG:This message should appear on the console
INFO:So should this
WARNING:And this, too
```
对于`%(levelname)s`这种东西，是`logging`模块内置的，可以被输出到日志中的对象，更多的内容在下面将会列举。

### 附加时间信息
要在日志内容中附加时间信息，可以在format字符串中添加%(asctime)s
```python
import logging
logging.basicConfig(format='%(asctime)s %(message)s')
logging.warning('is when this event was logged.')
```
输出结果
```log
2010-12-12 11:41:42,612 is when this event was logged.
```
默认情况下，时间的显示使用`ISO8601`格式。如果想做更深入的定制，可以提供`datefmt`参数，如下所示：
```python
import logging
logging.basicConfig(format='%(asctime)s %(message)s', datefmt='%m/%d/%Y %I:%M:%S %p')
logging.warning('is when this event was logged.')
```
输出结果:
```log
12/12/2010 11:46:36 AM is when this event was logged.
```
`datefmt`参数的定制和`time`模块的`time.strftime()`一样！

## 高级用法

