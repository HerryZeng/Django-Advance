## Django国际化和本地化

所谓的国际化，是指使用不同语言的用户在访问同一个网站页面时能够看到符合其自身语言的文本页面。
国际化的基本原理是：
    + 浏览器通过LANGUAGE_CODE在HTTP请求头中告诉网站后台服务器用户所需要的页面语言；
    + 网站服务器在渲染页面时，根据LANGUAGE_CODE查询每个需要翻译成对应语言的文本字符串，并将其替换到网页内，最后将网页返回给用户浏览器。

Django对文本翻译、日期格式、时间格式、数字格式和时区具有很好的支持。这些内容是国际化的主要工作对象。
从本质上，Django做了这么两件事：
    + 允许开发者和模板设计者指定在他们的app中哪些部分需要进行翻译或者格式化成当地的语言、习惯、用法和习俗；
    + 根据用户的偏好习惯，使用钩子，进行Web本地化。
    
面我们看一下Django是如何国际化的：

---

### 一、在视图中标识面要翻译的文本

在视图中和HTML模板中都可以标识要翻译的文本。在视图中，通过`_()`或`ugettext()`函数，指定某个变量需要翻译，如下所示：
```python
from django.utils.translation import ugettext as _
from django.http import HttpResponse

def my_view(request):
    output = _("Welcome to my site.")
    return HttpResponse(output)
```
这等同于：
```python
from django.utils.translation import ugettext
from django.http import HttpResponse

def my_view(request):
    output = ugettext("Welcome to my site.")
    return HttpResponse(output)
```
也等同于：
```python
def my_view(request):
    sentence = 'Welcome to my site.'
    output = _(sentence)
    return HttpResponse(output)
```
还可以这么用：
```python
def my_view(request, m, d):
    output = _('Today is %(month)s %(day)s.') % {'month': m, 'day': d}
    return HttpResponse(output)
```
如果你想给翻译人员一些提示，可以添加一个以Translators为前缀的注释，例如：
```python
def my_view(request):
    # Translators: This message appears on the home page only
    output = ugettext("Welcome to my site.")
```

---

### 二、在模板中表示同要翻译的文本

在模版文件中，要标识一个待翻译的文本，需要使用`{% trans %}`模板标签，但首先你要在模版的顶部加载`{% load i18n %}`。比如：
```html
{% load i18n %}
<title>{% trans "This is the title." %}</title>
<title>{% trans myvar %}</title>
```
要注意的是trans标签内部不可以有内嵌的模板变量

如果你想提前翻译字符串但是不显示出来，可以使用下面的方法：
```html
{% trans "This is the title" as the_title %}

<title>{{ the_title }}</title>
<meta name="description" content="{{ the_title }}">
```
上面的做法实际上相当于定义了几个模板变量，下面则是更加复杂的用法：
```html
{% trans "starting point" as start %}
{% trans "end point" as end %}
{% trans "La Grande Boucle" as race %}

<h1>
  <a href="/" title="{% blocktrans %}Back to '{{ race }}' homepage{% endblocktrans %}">{{ race }}</a>
</h1>
<p>
{% for stage in tour_stages %}
    {% cycle start end %}: {{ stage }}{% if forloop.counter|divisibleby:2 %}<br />{% else %}, {% endif %}
{% endfor %}
</p>
```

---

### 三、blocktrans模板标签

与`{% trans %}`模板标签不同，`blocktrans`标签允许你通过使用占位符来标记由文字和可变内容组成的复杂句子进行翻译，如下例所示：
```html
% blocktrans %}This string will have {{ value }} inside.{% endblocktrans %}
```
还可以像下面一样使用：
```html
{% blocktrans with amount=article.price %}
That will cost $ {{ amount }}.
{% endblocktrans %}

{% blocktrans with myvar=value|filter %}
This will have {{ myvar }} inside.
{% endblocktrans %}
```
甚至在一个blocktrans标签内内使用多个表达式：
```html
{% blocktrans with book_t=book|title author_t=author|title %}
This is {{ book_t }} by {{ author_t }}
{% endblocktrans %}
```

---

### 四、本地化