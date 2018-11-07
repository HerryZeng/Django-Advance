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