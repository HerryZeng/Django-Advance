# 常用的模板标签

1. `if`标签：`if`标签相当于`Python`的`if`语句，有`elif`和`else`相对应，但是所有的标签都需要用标签符号(`{% %}`)进行包裹。`if`标签中可以使用`==、!=、<、<=、>、>=、in、not in、is、is not`等判断运算符。示例代码如下：
```python
    {% if '张三' in persons %}
        <p>张三<p>
    {% else %}
        <p>李四</p>
    {% endif %}
```

2. `for ... in ...`标签：`for ... in ...`类似于`Python`中的`for ... in ...`。可以遍历列表、元组、字符串、字典等一切可以遍历的对象。示例代码如下：
```python
    {% for person in persons }%
        <p>{{ person.name }}</p>
    {% endfor %}
```
如果想要反向遍历，那么在遍历的时候就加上一个`reversed`。示例代码如下：
```python
    {% for person in persons reversed }%
        <p>{{ person.name }}</p>
    {% endfor %}
```
遍历字典的时候，需要使用`items`、`keys`和`values`等方法。在`DTL`中，**执行一个方法不能使用圆括号的形式**。遍历活字典示例代码如下：
```python
    {% for key,value in person.items %}
        <p>Key:{{ key }}</p>
        <p>Value:{{ value }}</p>
    {% endfor %}
```