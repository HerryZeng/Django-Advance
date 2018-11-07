### 三、ModelAdmin属性

15 . 指定显示在修改页面上的字段。这是一个很常用也是最重要的技巧之一。例如：
```python
list_display = ('first_name', 'last_name')
```
如果你不设置这个属性，admin站点将只显示一列，内容是每个对象的`__str__()`(Python2使用`__unicode__()`)方法返回的内容。

在list_display中，你可以设置四种值：
    + 模型的字段名
    ```python
    class PersonAdmin(admin.ModelAdmin):
    list_display = ('first_name', 'last_name')
    ```
    + 一个函数，它接收一个模型实例作为参数
    ```python
    def upper_case_name(obj):
        return ("%s %s" % (obj.first_name, obj.last_name)).upper()
    upper_case_name.short_description = 'Name'

    class PersonAdmin(admin.ModelAdmin):
        list_display = (upper_case_name,)
    ```
    + 一个表示ModelAdmin的某个属性的字符串
    类似上面的函数调用，通过反射获取函数名，换了种写法而已，例如：
    ```python
    class PersonAdmin(admin.ModelAdmin):
        list_display = ('upper_case_name',)

        def upper_case_name(self, obj):
            return ("%s %s" % (obj.first_name, obj.last_name)).upper()
        upper_case_name.short_description = 'Name'
    ```
    + 一个表示模型的某个属性的字符串
    类似第二种，但是此处的self是模型实例，引用的是模型的属性。参考下面的例子：
    ```python
    from django.db import models
    from django.contrib import admin
    
    class Person(models.Model):
        name = models.CharField(max_length=50)
        birthday = models.DateField()
    
        def decade_born_in(self):
            return self.birthday.strftime('%Y')[:3] + "0's"
        decade_born_in.short_description = 'Birth decade'
    
    class PersonAdmin(admin.ModelAdmin):
        list_display = ('name', 'decade_born_in')
    ```
    下面是对list_display属性的一些特别提醒：
        + 对于Foreignkey字段，显示的将是其`__str__()`方法的值。
        + 不支持ManyToMany字段。如果你非要显示它，请自定义方法。
        + 对于BooleanField或NullBooleanField字段，会用on/off图标代替True/False。
        + 如果给list_display提供的值是一个模型的、ModelAdmin的或者可调用的方法，默认情况下会自动对返回结果进行HTML转义，这可能不是你想要的。
    下面是一个完整的例子：
    ```python
    from django.db import models
    from django.contrib import admin
    from django.utils.html import format_html
    
    class Person(models.Model):
        first_name = models.CharField(max_length=50)
        last_name = models.CharField(max_length=50)
        color_code = models.CharField(max_length=6)
    
        def colored_name(self):
            # 关键是这句！！！！！请自己调整缩进。
            return '<span style="color: #%s;">%s %s</span>'%(
                self.color_code,
                self.first_name,
                self.last_name,
            )
    class PersonAdmin(admin.ModelAdmin):
        list_display = ('first_name', 'last_name', 'colored_name')
    ```
    
    实际的效果如下图所示：
    ![](../images/chapter12/006.png)
    
    
    很明显，你是想要有个CSS效果，但Django把它当普通的字符串了。怎么办呢？用`format_html()`或者`format_html_join()`或者`mark_safe()`方法！
    
    ```python
    from django.db import models
    from django.contrib import admin
    # 需要先导入！
    from django.utils.html import format_html
    
    class Person(models.Model):
        first_name = models.CharField(max_length=50)
        last_name = models.CharField(max_length=50)
        color_code = models.CharField(max_length=6)
    
        def colored_name(self):
            # 这里还是重点，注意调用方式，‘%’变成‘{}’了！
            return format_html(
                '<span style="color: #{};">{} {}</span>',
                self.color_code,
                self.first_name,
                self.last_name,
            )
    
    class PersonAdmin(admin.ModelAdmin):
        list_display = ('first_name', 'last_name', 'colored_name')
    ```
    