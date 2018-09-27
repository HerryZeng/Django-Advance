# 模型的操作

在 `ORM `框架中，所有模型相关的操作，比如添加/删除等。其实都是映射到数据库中一条数据的操作。因此模型操作也就是数据库表中数据的操作。

## 添加一个模型到数据库中

添加模型到数据库中。首先需要创建一个模型。创建模型的方式很简单，就跟创建普通的 `Python `对象是一摸一样的。在创建完模型之后，需要调用模型的 `save `方法，这样 `Django `会自动的将这个模型转换成 `sql `语句，然后存储到数据库中。示例代码如下：
```python
    class Book(models.Model):
        name = models.CharField(max_length=20,null=False)
        desc = models.CharField(max_length=100,name='description',db_column="description1")
        pub_date = models.DateTimeField(auto_now_add=True)
        book = Book(name='三国演义',desc='三国英雄！')
        book.save()
```

### 查找数据

查找数据都是通过模型下的 `objects `对象来实现的。

### 查找所有数据

要查找 `Book `这个模型对应的表下的所有数据。那么示例代码如下：
```python
    books = Book.objects.all()
```
以上将返回 Book 模型下的所有数据。