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
