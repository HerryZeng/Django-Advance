## 自定制Admin

如果只是在admin中简单的展示及管理模型，那么在admin.py模块中使用admin.site.register将模型注册一下就好了：
```python
from django.contrib import admin
from myproject.myapp.models import Author

admin.site.register(Author)
```
但是，很多时候这远远不够，我们需要对admin进行各种深度定制，以满足我们的需求。

这就要使用Django为我们提供的`ModelAdmin`类了。

`ModelAdmin`类是一个模型在admin页面里的展示方法，如果你对默认的admin页面满意，那么你完全不需要定义这个类，直接使用最原始的样子也行。通常，它们保存在app的admin.py文件里。下面是个简单的例子：
```python
from django.contrib import admin
from myproject.myapp.models import Author

# 创建一个ModelAdmin的子类
class AuthorAdmin(admin.ModelAdmin):
    pass

# 注册的时候，将原模型和ModelAdmin耦合起来
admin.site.register(Author, AuthorAdmin)
```

---

### 一、注册装饰器

除了常用的`admin.site.register(Author, AuthorAdmin)`方式进行注册，还可以用装饰器的方式连接模型和`ModelAdmin`。如下所示：
```python
from django.contrib import admin
from .models import Author

@admin.register(Author)
class AuthorAdmin(admin.ModelAdmin):
    pass
```
这个装饰器可以接收一些模型类作为参数，以及一个可选的关键字参数site（如果你使用的不是默认的AdminSite），比如。
```python
from django.contrib import admin
from .models import Author, Reader, Editor
from myproject.admin_site import custom_admin_site

@admin.register(Author, Reader, Editor, site=custom_admin_site)
class PersonAdmin(admin.ModelAdmin):
    pass
```

---

### 二、 搜索admin文件

当你在`INSTALLED_APPS`设置中添加了`django.contrib.admin`后，Django将自动在每个应用中搜索`admin`模块并导入它。也就是说，通常我们在每个`app`下都有一个`admin.py`文件，将当前`app`和`admin`有关的内容写到内部的`admin.py`文件中就可以了，Django会自动搜索并应用它们。
    + class apps.AdminConfig：admin默认的`AppConfig`类，当Django启动时自动调用其`autodiscover()`方法
    + class apps.SimpleAdminConfig：和上面的类似，但不调用`autodiscover()`
    + autodiscover()：自动搜索admin模块的方法。在使用自定义的site时，必须禁用这个方法，你应该在`INSTALLED_APPS`设置中用`django.contrib.admin.apps.SimpleAdminConfig`替代`django.contrib.admin`
    

---

### 三、ModelAdmin的属性

真正用来定制admin的手段，大部分都集中在这些ModelAdmin内置的属性上。
ModelAdmin非常灵活，它有许多内置属性，帮助我们自定义admin的界面和功能。所有的属性都定义在ModelAdmin的子类中，如下方式：
```python
from django.contrib import admin

class AuthorAdmin(admin.ModelAdmin):
    date_hierarchy = 'pub_date'
```

1. ModelAdmin.actions
一个列表，包含自定义的actions，后面有专门的叙述。

2. ModelAdmin.actions_on_top
是否在列表上方显示actions的下拉框，默认为True

3. ModelAdmin.actions_on_bottom
是否在列表下方显示actions的下拉框，默认为False。效果看下面的图片，没什么大用途。
![](../images/chapter12/001.png)

