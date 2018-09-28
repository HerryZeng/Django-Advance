# QuerySet API

我们通常做查询操作的时候，都是通过 `模型名字.objects` 的方式进行操作。其实 `模型名字.objects` 是一个 `django.db.models.manager.Manager` 对象，而 `Manager` 这个类是一个“空壳”的类，他本身是没有任何的属性和方法的。他的方法全部都是通过 `Python`动态添加的方式，从 `QuerySet`类中拷贝过来的。示例图如下：
![QuerySet API](../images/chapter04/QuerySet_API.png)
所以我们如果想要学习 `ORM`模型的查找操作，必须首先要学会 `QuerySet`上的一些 API 的使用。

## 返回新的QuerySet方法

在使用 `QuerySet`进行查找操作的时候，可以提供多种操作。比如过滤完后还要根据某个字段进行排序，那么这一系列的操作我们可以通过一个非常流畅的 `链式调用` 的方式进行。比如要从文章表中获取标题为 `123`，并且提取后要将结果根据发布的时间进行排序，那么可以使用以下方式来完
成：
```python
    articles = Article.objects.filter(title='123').order_by('create_time')
```
可以看到 `order_by`方法是直接在 `filter`执行后调用的。这说明 `filter`返回的对象是一个拥有 `order_by`方法的对象。而这个对象正是一个新的 `QuerySet`对象。因此可以使用 `order_by`方法。
那么以下将介绍在那些会返回新的 `QuerySet`对象的方法。
1. `filter`：将满足条件的数据提取出来，返回一个新的 `QuerySet`。具体的 filter 可以提供什么条件查询。请见查询操作章节。
2. `exclude`：排除满足条件的数据，返回一个新的 `QuerySet`。示例代码如下：
```python
Article.objects.exclude(title__contains='hello')
```
以上代码的意思是提取那些标题不包含 hello 的图书。
3. `annotate`：给 `QuerySet`中的每个对象都添加一个使用查询表达式（聚合函数、F表达式、Q表达式、Func表达式等）的新字段。示例代码如下：
```python
    articles = Article.objects.annotate(author_name=F("author__name"))
```
以上代码将在每个对象中都添加一个 author__name 的字段，用来显示这个文章的作者的年