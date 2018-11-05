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