### 三、ModelAdmin属性

18 . ModelAdmin.list_filter
设置`list_filter`属性后，可以激活修改列表页面的右侧边栏，用于对列表元素进行过滤，如下图：
![](../images/chapter12/011.png)

list_filter必须是一个元组或列表，其元素是如下类型之一：
    + 某个字段名，但该字段必须是BooleanField、CharField、DateField、DateTimeField、IntegerField、ForeignKey或者ManyToManyField中的一种。例如：
    ```python
    class PersonAdmin(admin.ModelAdmin):
        list_filter = ('is_staff', 'company')
    ```
    在这里，你可以利用双下划线进行跨表关联，如下例：
    ```python
    class PersonAdmin(admin.UserAdmin):
        list_filter = ('company__name',)
    ```
    
    + 一个继承django.contrib.admin.SimpleListFilter的类。你要给这个类提供title和parameter_name的值，并重写lookups和queryset方法。例如：
    
    ```python
    from datetime import date
    from django.contrib import admin
    from django.utils.translation import ugettext_lazy as _
    
    class DecadeBornListFilter(admin.SimpleListFilter):
        # 提供一个可读的标题
        title = _('出生年代')
    
        # 用于URL查询的参数.
        parameter_name = 'decade'
    
        def lookups(self, request, model_admin):
            """
            返回一个二维元组。每个元组的第一个元素是用于URL查询的真实值，
            这个值会被self.value()方法获取，并作为queryset方法的选择条件。
            第二个元素则是可读的显示在admin页面右边侧栏的过滤选项。        
            """
            return (
                ('80s', _('80年代')),
                ('90s', _('90年代')),
            )
            
        def queryset(self, request, queryset):
            """
            根据self.value()方法获取的条件值的不同执行具体的查询操作。
            并返回相应的结果。
            """
            if self.value() == '80s':
                return queryset.filter(birthday__gte=date(1980, 1, 1),
                                        birthday__lte=date(1989, 12, 31))
            if self.value() == '90s':
                return queryset.filter(birthday__gte=date(1990, 1, 1),
                                        birthday__lte=date(1999, 12, 31))

    class PersonAdmin(admin.ModelAdmin):
    
        list_display = ('first_name', 'last_name', "colored_first_name",'birthday')
    
        list_filter = (DecadeBornListFilter,)
    ```
    其效果如下图：
    ![](../images/chapter12/012.png)
    注意：为了方便，我们通常会将HttpRequest对象传递给lookups和queryset方法，如下所示：
    ```python
    class AuthDecadeBornListFilter(DecadeBornListFilter):

    def lookups(self, request, model_admin):
        if request.user.is_superuser:
            return super(AuthDecadeBornListFilter, self).lookups(request, model_admin)

    def queryset(self, request, queryset):
        if request.user.is_superuser:
            return super(AuthDecadeBornListFilter, self).queryset(request, queryset)
    ```
    同样的，我们默认将ModelAdmin对象传递给lookups方法。下面的例子根据查询结果，调整过滤选项，如果某个年代没有符合的对象，则这个选项不会在右边的过滤栏中显示：
    ```python
    class AdvancedDecadeBornListFilter(DecadeBornListFilter):

    def lookups(self, request, model_admin):
        """
        只有存在确切的对象，并且它出生在对应年代时，才会出现这个过滤选项。
        """
        qs = model_admin.get_queryset(request)
        if qs.filter(birthday__gte=date(1980, 1, 1),
                      birthday__lte=date(1989, 12, 31)).exists():
            yield ('80s', _('in the eighties'))
        if qs.filter(birthday__gte=date(1990, 1, 1),
                      birthday__lte=date(1999, 12, 31)).exists():
            yield ('90s', _('in the nineties'))
    ```
    + 也可以是一个元组。它的第一个元素是个字段名，第二个元素则是继承了django.contrib.admin.FieldListFilter的类。例如：
    ```python
    class PersonAdmin(admin.ModelAdmin):
        list_filter = (
            ('is_staff', admin.BooleanFieldListFilter),
        )
    ```
    你可以使用RelatedOnlyFieldListFilter限制关联的对象。假设author是关联User模型的ForeignKey，下面的用法将只选择那些出过书的user而不是所有的user：
    ```python
    class BookAdmin(admin.ModelAdmin):
        list_filter = (
            ('author', admin.RelatedOnlyFieldListFilter),
        )
    ```
    另外，其template属性可以指定渲染的模板，如下则指定了一个自定义的模板。（Django默认的模板为admin/filter.html）
    ```python
    class FilterWithCustomTemplate(admin.SimpleListFilter):
        template = "custom_template.html"
    ```