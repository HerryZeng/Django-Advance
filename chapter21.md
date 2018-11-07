## 序列化 serializers

Django的序列化工具让你可以将Django的模型‘翻译’成其它格式的数据。通常情况下，这种其它格式的数据是基于文本的，并且用于数据交换\传输过程。

---

### 一、序列化数据

Django为我们提供了一个强大的序列化工具serializers。使用它也很简单，如下所示：
```python
from django.core import serializers
data = serializers.serialize("xml", SomeModel.objects.all())
```
首先，从`djang.core`导入它，然后调用它的`serialize`方法，这个方法至少接收两个参数，第一个是你要序列化成为的数据格式，这里是‘xml’，第二个是要序列化的数据对象，数据通常是ORM模型的QuerySet，一个可迭代的对象。
就是这么简单！！

还有一种比较复杂，但钩子更多的使用方法，如下所示：
```python
XMLSerializer = serializers.get_serializer("xml")
xml_serializer = XMLSerializer()
xml_serializer.serialize(queryset)
data = xml_serializer.getvalue()
```
主要是使用了serializers的get_serializer()和getvalue()方法。

当你需要将序列化的数据保存到一个文件对象中的时候，上面的方式就非常有用，例如：
```python
with open("file.xml", "w") as out:
    xml_serializer.serialize(SomeModel.objects.all(), stream=out)
```

1. 序列化指定字段
如果你不想序列化模型对象所有字段的内容，只想序列化某些指定的字段，可以使用fields参数，如下所示：
```python
from django.core import serializers
data = serializers.serialize('xml', SomeModel.objects.all(), fields=('name','size'))
```
这样，只有name和size字段会被序列化。但是，有一个例外，模型的主键pk被隐式输出了，虽然它并不包含在fields参数中。

2. 序列化继承模型
考虑下面的模型继承：
```python
class Place(models.Model):
    name = models.CharField(max_length=50)

class Restaurant(Place):
    serves_hot_dogs = models.BooleanField(default=False)
```
如果你只序列化餐厅模型：
```python
data = serializers.serialize('xml', Restaurant.objects.all())
```
序列化输出上的字段将只包含`serves_hot_dogs`属性。基类的`name`属性并不会一起序列化。

为了完全序列化Restaurant实例，还需要将Place模型序列化，如下所示：
```python
all_objects = list(Restaurant.objects.all()) + list(Place.objects.all())
data = serializers.serialize('xml', all_objects)
```

---

### 二、反序列化数据

反序列化也相当简单，如下所示：
```python
for obj in serializers.deserialize("xml", data):
    do_something_with(obj)
```
其中的`data`是我们以前序列化后生成的数据（一个字符串或者数据流）。`deserialize()`方法返回的是一个迭代器，通过for循环，拿到它内部的每个元素。

在这里有区别的是，`deserialize`返回的迭代器对象不是简单的Django模型的对象。而是特殊的`DeserializedObject`实例。调用`DeserializedObject.save()`方法可以将对象保存到数据库。

PS：如果序列化数据中的pk属性不存在或为null，则新实例将保存到数据库。

将`DeserializedObject`保存到数据库，可以如下操作：
```python
for deserialized_object in serializers.deserialize("xml", data):
    if object_should_be_saved(deserialized_object):
        deserialized_object.save()
```
在这么做之前，你必须保证你的序列化数据是合乎你本地ORM模型属性的，否则保存的过程中会出现各种让你挠头的错误，这是很显然的。


---

### 可序列化的格式
Djanggo支持三种序列化格式，其中的一些可能需要安装第三方库支持：
    + xml
    + json
    + yaml
    
1. XML
xml格式相当简单：
```xml
<?xml version="1.0" encoding="utf-8"?>
<django-objects version="1.0">
    <object pk="123" model="sessions.session">
        <field type="DateTimeField" name="expire_date">2013-01-16T08:16:59.844560+00:00</field>
        <!-- ... -->
    </object>
</django-objects>
```
外键字段被序列化成下面的格式：
```xml
<object pk="27" model="auth.permission">
    <!-- ... -->
    <field to="contenttypes.contenttype" name="content_type" rel="ManyToOneRel">9</field>
    <!-- ... -->
</object>
```
多对多字段被序列化成下面的样子：
```xml
<object pk="1" model="auth.user">
    <!-- ... -->
    <field to="auth.permission" name="user_permissions" rel="ManyToManyRel">
        <object pk="46"></object>
        <object pk="47"></object>
    </field>
</object>
```

2. JSON
序列化成json格式后，看起来是下面的样子：
```json
[
    {
        "pk": "4b678b301dfd8a4e0dad910de3ae245b",
        "model": "sessions.session",
        "fields": {
            "expire_date": "2013-01-16T08:16:59.844Z",
            ...
        }
    }
]
```
需要注意的是，如果你的ORM模型具有自定义的字段，那么Django给我们提供的序列化工具，就无法正常工作，你必须自己编写相应部分的序列化代码，下面是一个参考的例子：
```python
from django.utils.encoding import force_text
from django.core.serializers.json import DjangoJSONEncoder

class LazyEncoder(DjangoJSONEncoder):
    def default(self, obj):
        if isinstance(obj, YourCustomType):
            return force_text(obj)
        return super(LazyEncoder, self).default(obj)
```
上面编写了一个LazyEncoder类，用来实现你的序列化方法，使用下面的方法调用它：
```python
from django.core.serializers import serialize

serialize('json', SomeModel.objects.all(), cls=LazyEncoder)
```
PS：Python本身不支持序列化类到json格式，Django帮我们实现了它自己的模型类序列化为json的方法，但也仅限于此，如果你在Django内写了一个别的自定义类，一样无法序列化为json格式，除非你自己实现，像上面的例子所示。

3. YAML
yaml的格式和json很像，如下所示：
```yaml
-   fields: {expire_date: !!timestamp '2013-01-16 08:16:59.844560+00:00'}
    model: sessions.session
    pk: 4b678b301dfd8a4e0dad910de3ae245b
```