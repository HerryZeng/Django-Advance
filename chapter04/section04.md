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

日期类型，在`Python`中是`datetime.date`类型，可以记录年月日。在映射到数据库中也`date`类型，使用这个`Field`可以传递以下几个参数：
1. `auto_now`：在每次这个数据保存的时候，都使用当前的时间。比如做为一个记录修改日期的字段，可以将这个属性设置为`True`。
2. `auto_now_add`：在每次数据第一次被添加进行的时候，都使用当前的时间，比如作为一个记录第一次入库的字段，可以将这个属性设置为`True`。

### DateTimeField

日期时间类型，类似于`DateField`，不仅仅可以存储日期，还可以存储时间。映射到数据库中是`datetime`类型。这个`Field`也可以用`auto_now`和`auto_now_add`这两个属性。

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

# 模型中`Meta`配置



