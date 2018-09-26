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