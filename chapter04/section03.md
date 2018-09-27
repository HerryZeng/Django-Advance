# ORM模型介绍

随着项目越来越大，采用写原生**SQL**的方式在代码中分出现大量的**SQL**语句，那么问题就出现了：
1. **SQL**语句的重复利用率不高，起复杂的**SQL**语句条件越多，代码越长。会出现很多相近的**SQL**语句。
2. 很多**SQL**语句是在业务逻辑中拉出来的，如果有数据库需要更改，就要去修改这些逻辑，这会很容易漏掉对某些**SQL**语句的修改。
3. 写**SQL**时容易忽略**Web**安全问题，给未来造成隐患，**SQL**注入。
4.`ORM`，全称`Object Relational Mapping`，中文称为**对象关系映射**，通过`ORM`我们可以通过类的方式去操作数据库，而不用写原生的**SQL**语句。通过把表映射成类，把行作实例，把字段作为属性，`ORM`在执行对象操作的时候最终还是会把对应的操作转换为数据库原生语句。使用`ORM`有许多优点：
1. 易用性：使用`ORM`做数据库的开发可以有效的减少重复**SQL**语句的概率，写出来的模型也更加直观、清晰。
2. 性能损耗小：`ORM`转换成底层数据库操作指令确实会有一些开销。全从实际的情况来看，这种性能损耗很少（不足5%），只要不是对性能有严苛的要求，综合考虑开发效率、代码的阅读性，带来的好处要远远大于性能损耗，而且项目越大作用越明显。
3. 设计灵活：可以轻松的写出复杂的查询。
4. 可移植性：`Django`封装了底层的数据库实现，支持多个关系数的引擎，包括流行的`MySQL`、`PostgreSQL`和`SQLite`。可以非常轻松的切换数据库。
![ORM](http://ww1.sinaimg.cn/large/006k72Wegy1fpq8p75pnaj30go0akt9b.jpg)


# 创建ORM模型

`ORM`模型一般都放在`APP`的`models.py`文件中，每个`APP`都可以拥有自己的模型。并且如果这个模型想要映射到数据库中，那么这个`APP`必须放在`settings.py`的`INSTALLED_APP`中进行安装。示例如下：
```python
    from django.db import models
    
    class Book(models.Model):
        name = models.CharField(max_length=20,null=False)
        author = models.CharField(max_lenght=20,nullFalse)
        pub_date = models.DataTimeField(default=datetime.now)
        price = models.FloatField(default=0)
```
以上是定义了一个模型。这个模型继承`django.db.models.Model`，如果这个模型想要映射到数据库中，就必须继承自这个类。这个模型以后映射到数据库中，表名是模型名称的小写形式，为`book`。在这个表中，有四个字段，一个为`name`，这个字段保存是书的名称，是`varchar`类型，最长不能超过20个字符 ，并且不能为空。第二个字段是作者名字，同样是`varchar`类型，最长不能超过20个字符 ，并且不能为空。第三个字段是出版时间，数据类型是`datetime`类型，默认是保存这本书的时间。第五个字段是本书的价格，是浮点类型。
还有一个字段我们没有写，就是主键`id`，在`Django`中，如果一模型没有定义主键，那么将会自动生成一个自动增长的`int`类型的主键，并且这个主键的名字为`id`。