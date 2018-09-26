# 常用的模板标签

1. `if`标签：`if`标签相当于`Python`的`if`语句，有`elif`和`else`相对应，但是所有的标签都需要用标签符号(`{% %}`)进行包裹。`if`标签中可以使用`==、!=、<、<=、>、>=、in、not in、is、is not`等判断运算符。示例代码如下：
```python
    {% if '张三' in persons %}
        <p>张三<p>
    {% else %}
        <p>李四</p>
    {% endif %}
```

2. `for ... in ...`