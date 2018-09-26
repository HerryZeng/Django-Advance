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
在`for`循环中，`DTL`提供了一些变量可供使用。这些变量如下：
    * `forloop.counter`：当前循环的下标，以1为起始值
    * `forloop.counter0`:当前循环的下村，以0为起始值
    * `forloop.revcounter`:当前循环的反向下标值。以1为最后一个元素的下标
    * `forloop.revcounter0`:当前循环的反向下标值。以0为最后一个元素的下标
    * `forloop.first`:是否为第一次遍历
    * `forloop.last`:是否为最后一次遍历
    * `forloop.parentloop`:如果有多个循环嵌套，那么这个属性代表的是上一级的`for`循环
    
3. `for ... in ... empty`标签：这个标签使用跟`for ... in ...`一样的，只不过是遍历的对象如果没有元素的情况下，会执行`empty`中的内容。示例代码如下：
```python
    {% for person in persons %}
        <li>{{ person }}</li>
    {% empty %}
        暂时还没有任何人
    {% endfor %}
```

4. `with`标签：在模板中定义变量，有时一个变量访问的时候比较复杂，那么可以先把这个复杂的变量缓存到一个变量上，以后就可以直接使用这个变量就可以了。示例代码如下：
```python
    context = {
        "persons":["张三","李四"]
    }
    
    {% with lisi=persons.1 %}
        <p> {{ lisi }}</p>
    {% endwith %}
```
有几点需要强烈注意：
    - 在`with`语句中定义的变量，只能在`{% with %} {% endwith %}`中使用，不能在这个标签外面使用。
    + 定义变量的时候，不能在等号左右两边留有空格。比如`{% with lisi = persons.1 %}`是错误的。
    * 还有另外一种写法同样也是支持的：
    ```python
        {% with persons.1 as lisi %}
            <p> {{ lisi }} </p>
        {% endwith %}
    ```
    
5. `url`标签：在模板中，要写一些`url`，比如某个`a`标签中需要定义`href`属性。如果通过硬编码的方式直接将这个`url`写死在里面也是可以的。但这样对以后项目维护可能不是一件好事。因此建议使用这种反转的方式来实现，类似`django`中的`reverse`一样。示例代码如下：
```python
    <a href ="{% url 'book:list %}">图书列表</a>
```
如果`url`反转的时候需要传递参数，那么可以在后面传递。但是参数分位置参数和关键字参数。位置参数和关键字参数不能同时使用。示例代码如下：
```python

    # path部分
    path('detail<book_id>/',views.book_detail,name='detail')
    
    # url反转，使用位置参数
    <a href="{% url 'book:detail 1 %}">图书详情</a>
    
    # url反转，使用位置参数
    <a href="{% url 'book:detail book_id=1 %}">图书详情</a>
```
如果想经在使用`url`标签反转的时候要传递查询字符串的参数，那么必须手动在后面添加。示例如下：
```html
    <a href="{% url 'book:detail' book_id=1 %}?page=1">图书详情</a>
```
如果需要传递多个参数，那么通过空格的方式进行分隔。示例如下：
```html
    <a href="{% url 'book:detail' book_id=1 page=2 %}">图书详情</a>
```

6. `spaceless`标签：移除**HTML**标签中的空白字符。包括空格、tab键、换行等。示例如下：
```python
    {% spaceless %}
        <p>
            <a href="foo/">Foo</a>
        </p>
    {% endspaceless %}
```
那么在渲染完成后，会变成以下的代码：
```html
    <p><a href="foo/">Foo</a></p>
```
`spaceless`只会移除**HTML**标签之间的空白字符。而不会移除标签与文本之间的空白字符。看以下代码：
```html
    {% spaceless %}
        <strong>
            Hello
        </strong>
    {% endspaceless %}
```
这个不会移除`strong`中的空白字符 。

7. `autoescape`标签：开户和关闭这个标签内元素的自动转义功能。自动转义是可以将一些特殊的字符，比如`<`转义成`HTML`语法能识别的字符，比如`<`会被转义成`&lt;`，而`>`会被自动转义成`&gt;`。模板中默认是已开户了自动转义的。`autoescape`的示例代码中下：
```python
    # 传递的上下文信息
    context = {
        "info":"<a href='www.baidu.com'>百度</a>"
    }
    
    # 在模板中关闭自动转义
    {% autoescape on %}
        {{ info }}
    {% endautoescape %}
```
那么就会显示百度的一个超链接。如把`on`换成`off`，那就会显示成一个普通的字符串。示例代码中下：
```html
    {% autoescape off %}
        {{ info }}
    {% endautoescape %}
```

8. `verbatim`标签：默认的`DTL`模板中是会去解析那些特殊字符的。比如`{%`和`%}`以及`{{`等。如果在你某个代码段中不想使用`DTL`的解析引擎。那么你可以把这个代码片段放在`verbatim`标签中。示例如下：
```html
    {% verbatim %}
    {{ if dying }} Still alive. {{/if}}
    {% endverbatim %}
```

9. 更多标签请参考官方文档：
[https://docs.djangoproject.com/en/2.1/ref/templates/builtins/](https://docs.djangoproject.com/en/2.1/ref/templates/builtins/ Djang官方文档)