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


## Field的常用参数

### null


### blank


### db_column


### default

### primary_key


### unique


# 模型中`Meta`配置



