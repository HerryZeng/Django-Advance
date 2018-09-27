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
以上将返回 `Book `模型下的所有数据。

### 数据过滤

在查找数据的时候，有时候需要对一些数据进行过滤。那么这时候需要调用 `objects `的 `filter `方法。实例代码如下：
```python
books = Book.objects.filter(name='三国演义')
> [<Book:三国演义>]
# 多个条件
books = Book.objects.filter(name='三国演义',desc='test')
```
调用 `filter `，会将所有满足条件的模型对象都返回。

### 获取单个对象

使用 `filter `返回的是所有满足条件的结果集。有时候如果只需要返回第一个满足条件的对象。那么可以使用 `get `方法。示例代码如下：
```
book = Book.objects.get(name='三国演义')
> <Book:三国演义>
```
当然，如果没有找到满足条件的对象，那么就会**抛出一个异常**。而 `filter `在没有找到满足条件的数据的时候，是**返回一个空的列表**。

### 数据排序

在之前的例子中，数据都是无序的。如果你想在查找数据的时候使用某个字段来进行排序，那么可以使用 `order_by `方法来实现。示例代码如下：
```python
books = Book.objects.order_by("pub_date")
```
以上代码在提取所有书籍的数据的时候，将会使用 `pub_date `从小到大进行排序。如果想要进行倒序排序，那么可以在 `pub_date `前面加一个负号。实例代码如下：
```
books = Book.objects.order_by("-pub_date")
```

### 修改数据

在查找到数据后，便可以进行修改了。修改的方式非常简单，只需要将查找出来的对象的某个属性进行修改，然后再调用这个对象的 `save `方法便可以进行修改。示例代码如下：
```python
from datetime import datetime
book = Book.objects.get(name='三国演义')
book.pub_date = datetime.now()
book.save()
```

### 删除数据

在查找到数据后，便可以进行删除了。删除数据非常简单，只需要调用这个对象的 `delete `方法即可。实例代码如下：
```python
book = Book.objects.get(name='三国演义')
book.delete()
```