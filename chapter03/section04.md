## 模板常用过滤器

在模板中，有时需要对一些数据进行处理以后才能使用。一般在`Python`中我们是通过函数的形式来完成的，而模板中，则是通过过滤器来实现的。过滤器使用的是`|`来使用。比如使用`add`过滤器，示例代码如下:
```html
    {{ value|add:"2" }}
```

### add

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

### cut

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

### date

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

### default

如果值被评估为`False`。比如`[]、""、None、{}`等这些在`if`判断中为`False`的值，都会使用`default`过滤器提供的默认值。示例代码如下：
```html
    {{ value|default:"nothing" }}
```
如果`value`是等于一个空的字符串。比如`""`，那么以上代码将会输出`nothins`。

### default_if_none

如果值是`None`，那么将会使用`default_if_none`提供的默认值。这个和`default`有区别，`default`是所有被评估为`False`的都会使用默认值 。而`default_if_none`则只有这个值是等于`None`的时候才会使用默认值。

### first

返回列表/元组/字符串中的第一个元素

## last

返回列表/元组/字符串中的第一个元素


### floatformat

使用四舍五入的方式格式化一个浮点类型。如果这个过滤器没有传递任何参数。那么只会在小数点后保留一个小数，如果小数后面全是0，那么只会保留整数。当然也可以传递一个参数，标识具体要保留几个小数。

1. 如果没有传递参数
<table>
<thead>
<th>value</th>
<th>模板代码</th>
<th>输出</th>
</thead>
<tbody>
<tr>
<td>34.23234</td>
<td>{{ value|floatformat}}</td>
<td>34.2</td>
</tr>
<tr>
<td>34.000</td>
<td>{{ value|floatformat}}</td>
<td>34</td>
</tr>
<tr>
<td>34.260</td>
<td>{{ value|floatformat}}</td>
<td>34.3</td>
</tr>
</tbody>
</table>
2.如果传递参数：
<table>
<thead>
<th>value</th>
<th>模板代码</th>
<th>输出</th>
</thead>
<tbody>
<tr>
<td>34.23234</td>
<td>{{ value|floatformat:3}}</td>
<td>34.232</td>
</tr>
<tr>
<td>34.000</td>
<td>{{ value|floatformat:3}}</td>
<td>34.000</td>
</tr>
<tr>
<td>34.260</td>
<td>{{ value|floatformat:3}}</td>
<td>34.260</td>
</tr>
</tbody>
</table>

### join

类似与`Python`中的`join`，将列表/元组/字符串用指定的字符进行拼接。示例如下：
```python
    {{ value|join:"/" }}
```
如果`value`等于`['a','b','c']`，那以上代码将输出`a/b/c`。

### length

获取一个列表/元组/字符串/字典的长度。示例代码如下：
```python
    {{ value|length }}
```
如果`value`是等于`['a','b','c']`,那以上代码将输出`3`。如果`value`为`None`，则输出`0`。

### lower

将值中所有字符全部转换成小写。
```python
    {{ value|lower }}
```
如果`value`是等于`Hello World`。则输出`hello world`。

## upper

类似于`lower`，只不过是将指定的字符串全部转换成大写。

## random

在被给出的列表/元组/字符串/字典中随机的选择一个值。
```pytohn
    {{ value|random }}
```
如果`value`是等于`['a','b','c']`,那以上代码会在列表中随机选择一个。

### safe

标记一个字符串是安全的，也即会关掉这个字符串的自动转义。
```python
    {{value|safe}}
```
如果`value`是一个不包含任何特殊字符的字符串，比如`<a>`这种，那以上代码就会把字符串正常的输入。如果`value`是一串`html`代码，那以上代码将会把这个`html`代码渲染到浏览器中。

### slice

类似于`Python`中的切片操作。
```python
    {{some_list|slice:"2:"}}
```
以上代码将会把`some_list`从`2`开始做切片操作。

### striptags

删除字符串中所有的`html`标签。
```python
    {{value|striptags}}
```
如果`value`是`<strong>hello world</strong>`，那以上代码将会输出`hello world`。


### truncatechars

如果给定的字符串长度超过了过滤器指定的长度。那么就会进行切割，并且会拼接三个点作为省略号。
```python
    {{value|truncatechars:5}}
```
如果`value`是等于`北京欢迎你~`，那输出 的结果是`北京...`。可能会想，为什么不是`北京欢迎你...`。因为三个点也占了三个字符。所以`北京`+三个点的字符长度就是**5**。


### truncatechars_html

类似于`truncatechars`，只不过是不会切割`html`标签。
```python
    {{ value|truncates_html:5 }}
```
如果`value`是`<p>北京欢迎你</p>`，那输出的是`<p>北京...</p>`。

## 自定义模板过滤器

虽然`DTL`给我们内置了许多好用的过滤器。但是有些时候还是不能满足我们的需求。因此`Django`给我们提供了一个接口，可以让我们自定义过滤器，实现自己的需求。

1. 模板过滤器必须要放在`APP`中，并且这个`APP`必须 要在`INSTALLED_APPS`中进行安装。然后再在这个`APP`下面创建一个`Python包`叫做`templatetags`。再在这个包下面创建一个`python`文件。比如`APP`的名字叫做`book`，那么项目结构如下：
```python
    - book
        - views.py
        - urls.py
        - models.py
        - templagetags
            - my_filter.py
```
2. 在创建了存储过滤器的文件后，接下来就是在这个文件中写过滤器了。过滤实际上就是python中的一个函数，只不过是把这个函数注册到模板库中，以后在模板中就可以使用这个函数 了。但是这个函数的参数有限制，第一个参数必须是这个过滤器需要处理的值，第二个参数可有可无，如果有，那么就在模板中可以传递参数。并且**过滤器的函数最多只能有两个参数**。在写完过滤器后，再使用`django.template.Library`对象注册进去。示例代码如下：
```python
    from django import template
    
    # 创建模板库对象
    register = template.Library()
    
    # 过滤器函数
    def mycut(value,mystr):
        return value.replace(mystr)
        
    # 将函数注册到模板库中
    register.filter("mycut",mycut)
```
3. 以后想要在模板中使用这个过滤器，就要在模板中`load`一下这个过滤器所有模块的名称（也就是这个python文件的名字）。

### 自定义时间计算过滤器

有时间经学会在朋友圈中可以看到条信息发表的时间，并不是具体的时间，而是距离现在多久，比如`刚刚`，`1分钟前`等。这个功能`DTL`是没有内置这样的过滤器的，因此我们可以自定义一个这样过滤器。示例代码如下：
```python
    # time_filter.py file
    
    from datetime import datetime
    from django import template
    
    register = template.Library()
    
    def time_since(value):
        """
        time距离现在的时间间隔
        1. 如果时间间隔小于1分钟以内，那么就显示'刚刚'
        2. 如果是大于1分钟小于1小时，那么就显示'xx分钟前'
        3. 如果是大于1小时小于24小时，那么就显示'xx小时前'
        4. 如果是大于24小时小于30天以内，那么就显示'xx天前'
        5. 否则就是显示具体的时候 '2018/10/10 13:14'
        """
        if isinstance(value,datetime):
            now = datetime.now()
```
在模板中使用的示例代码如下：
```html
    {% load time_filter %}
    ...
    {% value | time_since %}
```
为了更加方便的将函数注册到模板库中当作过滤器，也可以使用装饰器来将一个函数包装成过滤器。示例代码如下：
```python
    from django import template
    register = template.Library()
    
    @register.filter(name='mycut')
    def mycut(value,mystr):
        return value.replace(mystr,"")
```

###默认的过滤器

`django.template.defaultfilters`

### 人类可读性

一些Django的‘奇技淫巧’就存在于这些不起眼的地方。

为了提高模板系统对人类的友好性，Django在`django.contrib.humanize`中提供了一系列的模板过滤器，有助于为数据展示添加“人文关怀”。

需要把`django.contrib.humanize`添加到`INSTALLED_APPS`设置中来激活这些过滤器。然后在模板中使用`{% load humanize %}`标签，就可以使用下面的过滤器了。

1. apnumber
对于数字1~9，返回英文单词，否则返回数字本身。 这遵循了出版图书的格式。
例如：
```
    1 会变成one。
    2 会变成 two。
    10 会变成 10
```
可以传递整数，或者整数的字符串形式。
2. intcomma
将整数或浮点数（或两者的字符串表示形式）转换为每隔三位数字包含逗号的字符串。这在财务报表中很有用。

例如：
```
    4500 会变成 4,500。
    4500.2变为4,500.2。
    45000 会变成 45,000
    450000 会变成 450,000。
    4500000 会变成 4,500,000。
```
如果启动了`Format localization`，还将遵循用户本地国家标准。例如，在德语（'de'）中：
```
    45000 会变成 '45.000'。
    450000 会变成 '450.000'。
``` 
3 . intword
将大整数（或整数的字符串表示形式）转换为友好的文本表示形式。适用于超过一百万的数字。
```
    1000000 会变成 1.0 million。
    1200000 会变成 1.2 million。
    1200000000 会变成 1.2 billion。
```
支持高达10的100次方 (Googol) 的整数。

如果启动了Format localization，还将遵循用户本地国家标准。例如，在德语（'de'）中：
```
    1000000 会变成 '1,0 Million'。
    1200000 会变成 '1,2 Million'。
    1200000000 会变成 '1,2 Milliarden'。
```
4 . naturalday
对于当天或者一天之内的日期，返回“today”,“tomorrow”或者“yesterday”的表示形式，视情况而定。否则，使用传进来的格式字符串进行日期格式化。

例如（“今天”是2007年2月17日）：
```
    16 Feb 2007 会变成 yesterday。
    17 Feb 2007 会变成 today。
    18 Feb 2007 会变成 tomorrow。
```
其它的日期，还是按照传统的方法展示。
5. naturaltime
对于日期时间的值，返回一个字符串来表示多少秒、分钟或者小时之前。如果超过一天之前，则回退为使用timesince格式。如果是未来的日期时间，返回值会自动使用合适的文字表述。

例如（“现在”是2007年2月17日16时30分0秒）：
```
17 Feb 2007 16:30:00 会变成 now。
17 Feb 2007 16:29:31 会变成 29 seconds ago。
17 Feb 2007 16:29:00 会变成 a minute ago。
17 Feb 2007 16:25:35 会变成 4 minutes ago。
17 Feb 2007 15:30:29 会变成 59 minutes ago。
17 Feb 2007 15:30:01 会变成 59 minutes ago。
17 Feb 2007 15:30:00 会变成 an hour ago。
17 Feb 2007 13:31:29 会变成 2 hours ago。
16 Feb 2007 13:31:29 会变成 1 day, 2 hours ago。
16 Feb 2007 13:30:01 会变成 1 day, 2 hours ago。
16 Feb 2007 13:30:00 会变成 1 day, 3 hours ago。
17 Feb 2007 16:30:30 会变成 30 seconds from now。
17 Feb 2007 16:30:29 会变成 29 seconds from now。
17 Feb 2007 16:31:00 会变成 a minute from now。
17 Feb 2007 16:34:35 会变成 4 minutes from now。
17 Feb 2007 17:30:29 会变成 an hour from now。
17 Feb 2007 18:31:29 会变成 2 hours from now。
18 Feb 2007 16:31:29 会变成 1 day from now。
26 Feb 2007 18:31:29 会变成 1 week, 2 days from now。
```
6 . ordinal
将一个整数转化为它的序数词字符串。
```
1 会变成 1st。
2 会变成 2nd。
3 会变成 3rd。
```

### 特殊的标签和过滤器

国际化标签和过滤器
Django还提供了一些模板标签和过滤器，用以控制模板中国际化的每个方面。它们允许对翻译，格式化和时区转换进行粒度控制。

1. `i18n`
此标签允许在模板中指定可翻译文本。要启用它，请将`USE_I18N`设置为`True`，然后加载`{％ load i18n ％}`。

2. `l10n`
此标签提供对模板的本地化控制，只需要使用`{％ load l10n ％}`。通常将`USE_L10N`设置为`True`，以便本地化默认处于活动状态。

3. `tz`
此标签对模板中的时区进行控制。 像`l10n`，只需要使用`{％ load tz }`，但通常还会将`USE_TZ`设置为`True`，以便默认情况下转换为本地时间。

其他标签和过滤器
Django附带了一些其他模板标签，必须在INSTALLED_APPS设置中显式启用，并在模板中启用{% load %}标记。

1. `django.contrib.humanize`
一组Django模板过滤器，用于向数据添加“人性化”，更加可读。

2. `static`
``static``标签用于链接保存在STATIC_ROOT中的静态文件。例如：
```html
{% load static %}
<img src="{% static "images/hi.jpg" %}" alt="Hi!" />
```
还可以使用变量：
```html
{% load static %}
<link rel="stylesheet" href="{% static user_stylesheet %}" type="text/css" media="screen" />
```
还可以像下面这么使用：
```html
{% load static %}
{% static "images/hi.jpg" as myphoto %}
<img src="{{ myphoto }}"></img>
```