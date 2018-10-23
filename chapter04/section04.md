# 模型常用字段

## 常用字段

在`Django`中，定义了一些`Field`来与数据库表中的字段类型进行映射。以下将介绍那些常用的字段类型。

### AutoField

映射到数据库中的`int`类型，可以有自动增长的特性，一般不需要使用这个类型。如果不指定主键，那么模型会自动的生成一个叫做`id`的自动增长的主键。如果你想指定一个其名字的并且具有自动增长的主键，使用`AutoField`也是可以的。

### BigAutoField

64位的整形，类似于`AutoField`，只不过是产生的数据范围是从`1-9223372036854775807`。

### BooleanField

在模型层接收的是`True/False`，在数据库层而是`tinyint`类型。如果没有指定默认值，默认值是`None`。

### CharField

在数据库层面是`varchar`类型，在`Python`层面变是普通的字符串。**这个类型在使用的时候必须要指定最大的长度**,也即必须要传递`max_length`这个关键字参数进去。

### DateField

在使用`DataeField`之前，先需要了解两个概念

#### navie时间

`navie`时间：不知道自己的时间表示的是哪个时区的。

#### aware时间

`aware`时间：

#### pytz库

专门用来处理时区的库，这个库经常会更新一些时区的数据，这个库在安装`Django`的时候会默认的安装。

#### astimezone方法

将一个时区的时间转换成另一个时区的时间。这个方法只能被`aware`类型的时间调用，不能被`navie`类型的时间调用。

```python
import pytz
from datetime import datetime

now = datetime.now()
utc_timezone=pytz.timezone('UTC')
bj_timezone=pytz.timezone('Asia/Shanghai')
now_utc.astimezone(bj_timezone)

```

#### replace方法

可以将一个时间的某些属性进行更改

```python
In [23]: now                                                                                                
Out[23]: datetime.datetime(2018, 10, 23, 14, 53, 29, 361613)

In [24]: now = now.replace(tzinfo=pytz.timezone("Asia/Shanghai"))                                           

In [25]: now                                                                                                
Out[25]: datetime.datetime(2018, 10, 23, 14, 53, 29, 361613, tzinfo=<DstTzInfo 'Asia/Shanghai' LMT+8:06:00 STD>)
```

#### django.utils.timezone.now()方法

1. 在`settings.py`配置文件中，如果`USE_TZ = True`, 此方法获取的是`aware`类型的时间，时间的时区是`UTC`，即
`2018-10-23 07:47:03.751990+00:00`。

2. 在`settings.py`配置文件中，如果`USE_TZ = False`,此方法获取的是`navie`类型的时间，即`datetime.now()`。

#### django.utils.timezone.localtime方法

1. 在`settings.py`配置文件中，如果`USE_TZ = True`, 此方法获取的是`aware`类型的时间，时间的时区是`settingspy`中设置的`TIME_ZONE`，即
`2018-10-23 15:47:03.751990+08:00`。

2. 在`settings.py`配置文件中，如果`USE_TZ = False`,此方法不可用。如果用会报错`localtime() cannot be applied to a naive datetime`

#### navie和aware介绍以及在django中的用法

[https://docs.djangoproject.com/en/2.1/topics/i18n/timezones/](https://docs.djangoproject.com/en/2.1/topics/i18n/timezones/)

日期类型，在`Python`中是`datetime.date`类型，可以记录年月日。在映射到数据库中也`date`类型，使用这个`Field`可以传递以下几个参数：
1. `auto_now`：在每次这个数据保存的时候，都使用当前的时间。比如做为一个记录修改日期的字段，可以将这个属性设置为`True`。
2. `auto_now_add`：在每次数据第一次被添加进行的时候，都使用当前的时间，比如作为一个记录第一次入库的字段，可以将这个属性设置为`True`。

### DateTimeField

日期时间类型，类似于`DateField`，不仅仅可以存储日期，还可以存储时间。映射到数据库中是`datetime`类型。这个`Field`也可以用`auto_now`和`auto_now_add`这两个属性。

1. `auto_now_add=True`: 是在第一交从汪厍数据进去的时候自动获取当前的时间

2. `auto_now=True`: 每次这个对象调用save方法的时候都会将当前的时间更新

### TimeField

时间类型，在数据库中是`time`类型，在`Python`中是`datetime.datetime`类型。

### EmailField

类似于`CharField`，在数据库底层也是一个`varchar`类型，最大长度是**254**个字符。

### FileField

用来存储文件的，这个可以参考后面的文件上传章节部分。

### ImageField

用来存储图片文件的，这个可以参考后面的图片上传章节部分。

### FloatField

浮点类型，映射到数据库中是`float`类型。

### IntegerField

整形，值的区间：`-2147483648-2147483647`。

### BigIntegerField

大整形，值的区间：`-9223372036854775808-9223372036854775807`。


### PositiveIntegerField

正整形，值的区间：`0-2147483647`。

### SmallIntegerField

小整形，值的区间：`-32768-32767`。

### PositiveSmallIntegerField

正小整形，值的区间：`0-32767`。

### TextField

大量的文本类型，映射到数据库中的是`longtext`类型。

### UUIDField

只能存储`uuid`格式的字符串，`uuid`是一个**32**位的全球唯一的字符串，一般用来作为主键。

### URLField

类似于`CharField`，只不过只能用来存储`url`格式的字符串，并且默认的`max_length`是**200**。


## Field的常用参数

### null

如果设置为`True`，`Django`将会映射表的时候指定是否为空。默认是`False`。在使用字符串相关的`Field`**（CharField/TextField）**的时候，官方推荐尽量不要使用这个参数，也就是保持默认值`False`。因为`Django`在处理字符串相关的`Field`的时候，即使这个`Field`的`null=False`，如果你没有给这个`Field`传递任何值，那么`Django`也会傅一个空的字符串`""`来作为默认值存储进去。因此如果再使用`null=True`，`Django`会产生两种空值的情况(**NULL**或空字符串).如果想要在表单验证的时候允许这个字符串为空，那么建议使用`blank=True`。如果你的`Field`是`BooleanField`，那么对就的可空的字段则为`NullBooleanField`。

### blank

标识这个字段在表单验证的时候是否可以为空。默认为`False`。这个和`null`是有区别的，`null`是一个纯数据库级别的。而`blank`是表单验证级别的。

### db_column

这个字段在数据库中的名字。如果没有设置这个参数，那么将会使用模型中属性的名称。

### default

默认值，可以为一个值，或一个函数，但不支持`lambda`表达式。并且不支持列表/字典/集合等可变的数据结构 。

### primary_key

是否为主键，默认是`False`。

### unique

在表中这个字段的值是否唯一，一般是设置手机号/邮箱等。

更多`Feild`参数请参考官方文档：
[https://docs.djangoproject.com/zh-hans/2.1/ref/models/fields/](https://docs.djangoproject.com/zh-hans/2.1/ref/models/fields/)

# 模型中`Meta`配置

对于一些模型级别的配置，我们可以在模型中定义一个类，叫做`Meta`。然后在这个类中清加一些类属性来控制模型的作用。比如我们杨要在数据库映射的时候使用自己指定的表名，而不是使用模型的名称。那么我们可以在`Meta`类中添加一个`db_table`的属性。示例代码如下：
```python
    class Book(models.Model):
        name = models.CharField(max_length=20,null=False)
        desc = models.CharField(max_length=100,name='description',db_column='description1')
        
        class Meta:
            db_table = 'book_model'
```
以下将对`Meta`类中的一些常用配置进行解释。


### db_table

这个模型映射到数据库中的表名，如果没有指定这个参数，那么在映射的时候会使用模型名来作为默认的表名。

### ordering

设置在提取数据的排序方式，后面章节中会讲到如何查找数据。比如我想在查找数据的时候根据添加的时间排序，示例如下：
```python
    class Book(models.Model):
        name = models.CharField(max_length=20,null=False)
        desc = models.CharField(max_length=100,name='description',db_column='description1')
        pub_date = models.DateTimeField(auto_now_add=True)
        
        class Meta:
            db_table = 'book_model'
            ordering = ['pub_date']
```
更多的配置后面会慢慢介绍到，官方文档：
[https://docs.djangoproject.com/en/2.1/ref/models/options/](https://docs.djangoproject.com/en/2.1/ref/models/options/)




