# 模板常用过滤器

在模板中，有时需要对一些数据进行处理以后才能使用。一般在`Python`中我们是通过函数的形式来完成的，而模板中，则是通过过滤器来实现的。过滤器使用的是`|`来使用。比如使用`add`过滤器，示例代码如下:
```html
    {{ value|add:"2" }}
```

## add

将传进来的参数添加原来的值上面。这个过滤器会尝试将`值`和`参数`转换成整形然后进行相加。如果转换成整形过程中失败了，那么会将`值`和`参数`进行拼接。如果是字符串，那么会拼接成字符串，如果是列表，那么会拼接成一个列表。示例代码如下：
```html
    {{ value|add:"2" }}
```
如果`value`是等于**4**，那么结果将是**6**。如果`value`是等于一个普通的字符器，如`abc`，那么结果将是`abc2`。`add`过滤器的源代码如下：
```python
    def add(value,arg):
    '''Add the arg to the value.'''
    try:
        return int(value) + int(arg)
    except (ValueError,TypeError):
        try:
            return value + arg
        except Exception
            return ''
```

## cut

移除值中所有指定的字符串。类似于`Python`中的`replace(arg,"")`。示例如下：
```python
    {{ value|cut:" " }}
```
以上示例将会移除`value`中所有的空格字符。`cut`过滤器的源代码如下：
```python
    def cut(value,arg):
        """Remove all values of arg from the given string."""
        safe = isinstance(value,SafeData)
        value = value.replace(arg,'')
        if save and arg != ';':
            return mark_safe(value)
        return value
```

## date

将一个日期按照指定的格式，格式化成字符串。示例如下：
```python
    # 数据
    context = {
        "birthday":datetime.now()
    }
    
    # 模板
    {{ birthday|date:"Y/m/d" }}
```
那么将会输出`2018/09/26`。其中`Y`代表的是四位数字的年份，`m`代表的是两位数字的月份，`d`代码的是两位数字的日。
还有更多的时间格式化的方式，见下表：
<table>
    <thead>
        <th>格式字符</th>
        <th>描述</th>
        <th>示例</th>
    </thead>
    <tbody>
        <tr>
            <td>Y</td>
            <td>四位数字的年份</td>
            <td>2018</td>
        </tr>
        <tr>
            <td>m</td>
            <td>两位数字的月份</td>
            <td>01-12</td>
        </tr>
        <tr>
            <td>d</td>
            <td>两位数字的日</td>
            <td>01-31</td>
        </tr>
        <tr>
            <td>n</td>
            <td>月份，1-9前面没有0前缀</td>
            <td>1-12</td>
        </tr>
        <tr>
            <td>j</td>
            <td>天，1-9前面没有0前缀</td>
            <td>1-31</td>
        </tr>
        <tr>
            <td>g</td>
            <td>小时，12小时格式的，1-9前面没有0前缀</td>
            <td>1-12</td>
        </tr>
        <tr>
            <td>h</td>
            <td>小时，12小时格式的，1-9前面有0前缀</td>
            <td>01-12</td>
        </tr>
        <tr>
            <td>G</td>
            <td>小时，24小时格式的，1-9前面没有0前缀</td>
            <td>1-23</td>
        </tr>
        <tr>
            <td>H</td>
            <td>小时，24小时格式的，1-9前面有0前缀</td>
            <td>01-23</td>
        </tr>
        <tr>
            <td>i</td>
            <td>分钟，1-9前面有0前缀</td>
            <td>00-59</td>
        </tr>
        <tr>
            <td>s</td>
            <td>秒，1-9前面有0前缀</td>
            <td>00-59</td>
        </tr>
    </tbody>
</table>