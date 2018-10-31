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

一旦标记好需要翻译的文本（也就是国际化）后，就需要进行本地化，也就是创建翻译用的语言文件。

语言文件（Language File）是Django用于保存翻译关系的文件，你的网站应该为每种支持的语言建立一个语言文件。

建立语言文件是通过`django-admin makemessages`命令完成的。

在项目的根目录下，也就是包含manage.py的目录下，运行下面的命令：
```bash
django-admin makemessages -l de
```
其中的`de`表示你要本地化的国家，例如`pt_BR`表示巴西葡萄牙语，奥地利德语为`de_AT`，印尼语为`id`。
或者使用下面的方式：
```bash 
python manage.py makemessages -l zh-cn  //中文简体
python manage.py makemessages -l en    //英文
```
执行命令后，Django会在根目录及其子目录下搜集所有需要翻译的字符串，默认情况下它会搜索.html、.txt和.py文件，然后在根目录的`locale/LANG/LC_MESSAGES`目录下创建一个`django.po`文件。对于上面的例子，目录就是`locale/de/LC_MESSAGES/`，文件就是`locale/de/LC_MESSAGES/django.po`。

**注意：在Windows下，需要提前安装GNU gettext工具！**

否则会弹出下面的错误：
```log
CommandError: Can't find msguniq. Make sure you have GNU gettext tools 0.15 or newer installed.
```
.po文件的格式非常简单！

每个.po文件首先包含一小部分元数据，例如翻译维护者的联系信息，但文件的大部分是翻译对照：被翻译字符串和特定语言的实际翻译文本之间的简单映射。

例如，有一个像下面这样的待翻译字符串：
```python
_("Welcome to my site.")
```
在.po文件中将包含一条下面样子的条目：
```python
#: path/to/python/module.py:23
msgid "Welcome to my site."
msgstr ""
```
这三行内容各自代表下面的意思：
    + 第一行通过注释表达该条要翻译的字符串在视图或模版中的位置；
    + msgid：要翻译的字符串。不要修改它。
    +msgstr：翻译后的文本。一开始它是空的，需要翻译人员逐条填写。
这是一个文本文件，需要专业的翻译人员将所有的msgstr空白‘填写’齐全。如果你的项目比较大，这可能是个磨人的事。

---

### 编译语言文件

当完成语言文件的创建和翻译工作后，或者对语言文件修改后，需要将其编译成对应的*.mo文件，Django在运行时将使用*.mo文件对网站进行国际化翻译。

进入项目根目录，运行下面的命令，进行语言文件编译：
```bash
django-admin compilemessages
```
Django将自动搜索所有的.po文件，将它们都翻译成.mo文件。

至此，国际化和本地化就完成了。你的网站页面将根据访问者使用语言的不同，展示为不同的语言版本，比如中文、英文、法文、德文之类。

---

### 附件

下面列出了所有可用于各种系统设置的语言代码对照表：
+ af 南非荷兰语
+ sq 阿尔巴尼亚语
+ ar-sa 阿拉伯语(沙特阿拉伯)
+ ar-iq 阿拉伯语(伊拉克)
+ ar-eg 阿拉伯语(埃及)
+ ar-ly 阿拉伯语(利比亚)
+ ar-dz 阿拉伯语(阿尔及利亚)
+ ar-ma 阿拉伯语(摩洛哥)
+ ar-tn 阿拉伯语(突尼斯)
+ ar-om 阿拉伯语(阿曼)
+ ar-ye 阿拉伯语(也门)
+ ar-sy 阿拉伯语(叙利亚)
+ ar-jo 阿拉伯语(约旦)
+ ar-lb 阿拉伯语(黎巴嫩)
+ ar-kw 阿拉伯语(科威特)
+ ar-ae 阿拉伯语(阿拉伯联合酋长国)
+ ar-bh 阿拉伯语(巴林)
+ ar-qa 阿拉伯语(卡塔尔)
+ eu 巴斯克语
+ bg 保加利亚语
+ be 贝劳语
+ ca 加泰罗尼亚语
+ zh-tw 中文(中国台湾)
+ zh-cn 中文(中华人民共和国)
+ zh-hk 中文(中国香港特别行政区)
+ zh-sg 中文(新加坡)
+ hr 克罗地亚语
+ cs 捷克语
+ da 丹麦语
+ nl 荷兰语(标准)
+ nl-be 荷兰语(比利时)
+ en 英语
+ en-us 英语(美国)
+ en-gb 英语(英国)
+ en-au 英语(澳大利亚)
+ en-ca 英语(加拿大)
+ en-nz 英语(新西兰)
+ en-ie 英语(爱尔兰)
+ en-za 英语(南非)
+ en-jm 英语(牙买加)
+ en 英语(加勒比)
+ en-bz 英语(伯利兹)
+ en-tt 英语(特立尼达)
+ et 爱沙尼亚语
+ fo 法罗语
+ fa 波斯语
+ fi 芬兰语
+ fr 法语(标准)
+ fr-be 法语(比利时)
+ fr-ca 法语(加拿大)
+ fr-ch 法语(瑞士)
+ fr-lu 法语(卢森堡)
+ gd 盖尔语(苏格兰)
+ gd-ie 盖尔语(爱尔兰)
+ de 德语(标准)
+ de-ch 德语(瑞士)
+ de-at 德语(奥地利)
+ de-lu 德语(卢森堡)
+ de-li 德语(列支敦士登)
+ el 希腊语
+ he 希伯来语
+ hi 北印度语
+ hu 匈牙利语
+ is 冰岛语
+ in 印度尼西亚语
+ it 意大利语(标准)
+ it-ch 意大利语(瑞士)
+ ja 日语
+ ko 朝鲜语
+ ko 朝鲜语(韩国)
+ lv 拉脱维亚语
+ lt 立陶宛语
+ mk FYRO 马其顿语
+ ms 马来西亚语 
+ mt 马耳他语
+ no 挪威语(博克马尔)
+ no 挪威语(尼诺斯克)
+ pl 波兰语
+ pt-br 葡萄牙语(巴西)
+ pt 葡萄牙语(葡萄牙)
+ rm 拉丁语系
+ ro 罗马尼亚语
+ ro-mo 罗马尼亚语(摩尔达维亚)
+ ru 俄语
+ ru-mo 俄语(摩尔达维亚)
+ sz 萨摩斯语(拉普兰)
+ sr 塞尔维亚语(西里尔)
+ sr 塞尔维亚语(拉丁)
+ sk 斯洛伐克语
+ sl 斯洛文尼亚语
+ sb 索布语
+ es 西班牙语(西班牙传统)
+ es-mx 西班牙语(墨西哥)
+ es 西班牙语(西班牙现代)
+ es-gt 西班牙语(危地马拉)
+ es-cr 西班牙语(哥斯达黎加)
+ es-pa 西班牙语(巴拿马)
+ es-do 西班牙语(多米尼加共和国)
+ es-ve 西班牙语(委内瑞拉)
+ es-co 西班牙语(哥伦比亚)
+ es-pe 西班牙语(秘鲁)
+ es-ar 西班牙语(阿根廷)
+ es-ec 西班牙语(厄瓜多尔)
+ es-cl 西班牙语(智利)
+ es-uy 西班牙语(乌拉圭)
+ es-py 西班牙语(巴拉圭)
+ es-bo 西班牙语(玻利维亚)
+ es-sv 西班牙语(萨尔瓦多)
+ es-hn 西班牙语(洪都拉斯)
+ es-ni 西班牙语(尼加拉瓜)
+ es-pr 西班牙语(波多黎各)
+ sx 苏图语
+ sv 瑞典语
+ sv-fi 瑞典语(芬兰)
+ th 泰语
+ ts 汤加语
+ tn 瓦纳语
+ tr 土耳其语
+ uk 乌克兰语
+ ur 乌尔都语
+ ve 文达语
+ vi 越南语
+ xh 科萨语
+ ji 依地语
+ zu 祖鲁语