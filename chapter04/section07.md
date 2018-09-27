# 查询操作

查找是数据库操作中一个非常重要的技术。查询一般就是使用 `filter `、 `exclude `以及 `get `三个方法来实现。我们可以在调用这些方法的时候传递不同的参数来实现查询需求。在 `ORM `层面，这、些查询条件都是使用 `field + __ + condition` 的方式来使用的。以下将那些常用的查询条件来一一解释。

## 查询条件

### exact

使用精确的`= `进行查找。如果提供的是一个 `None `，那么在 `SQL `层面就是被解释为 `NULL `。示例代码如下：
```python
    article = Article.objects.get(id__exact=14)
    article = Article.objects.get(id__exact=None)
```
以上的两个查找在翻译为 SQL 语句为如下：
```sql
    select ... from article where id=14;
    select ... from article where id IS NULL;
```

### iexact

使用 `like `进行查找。示例代码如下：
```python
    article = Article.objects.filter(title__iexact='hello world')
```
那么以上的查询就等价于以下的 `SQL `语句：
```sql
    select ... from article where title like 'hello world';
```
注意上面这个 `sql `语句，因为在 `MySQL `中，没有一个叫做 `ilike `的。所以 `exact `和 `iexact `的区别实际上就是 `LIKE `和 `= `的区别，在大部分 `collation=utf8_general_ci` 情况下都是一样的（ `collation `是用来对字符串比较的）。

### contains

**大小写敏感**，判断某个字段是否包含了某个数据。示例代码如下：
```python
    articles = Article.objects.filter(title__contains='hello')
```
在翻译成 `SQL `语句为如下：
```sql
    select ... where title like binary '%hello%';
```
要注意的是，在使用 `contains `的时候，翻译成的 `sql `语句左右两边是有百分号的，意味着使用的是模糊查询。而 `exact `翻译成 `sql `语句左右两边是没有百分号的，意味着使用的是精确的查询。

### icontains

**大小写不敏感**的匹配查询。示例代码如下：
```python
    articles = Article.objects.filter(title__icontains='hello')
```
在翻译成 `SQL `语句为如下：
```sql
    select ... where title like '%hello%';
```

### in

提取那些给定的 `field `的值是否在给定的容器中。容器可以为 `list `、 `tuple `或者任何一个可以迭代的对象，包括 `QuerySet `对象。示例代码如下：
```python
    articles = Article.objects.filter(id__in=[1,2,3])
```
以上代码在翻译成 `SQL `语句为如下：
```sql
    select ... where id in (1,3,4)
```
当然也可以传递一个 `QuerySet `对象进去。示例代码如下：
```python
    inner_qs = Article.objects.filter(title__contains='hello')
    categories = Category.objects.filter(article__in=inner_qs)
```
以上代码的意思是获取那些文章标题包含 `hello `的所有分类。将翻译成以下 `SQL `语句，示例代码如下：
```sql
    select ...from category where article.id in (select id from article where title like '%hello%');
```

### gt

某个 `field `的值要大于给定的值。示例代码如下：
```python
    articles = Article.objects.filter(id__gt=4)
```
以上代码的意思是将所有 id 大于4的文章全部都找出来。将翻译成以下 SQL 语句：
```sql
    select ... where id > 4;
```

### gte

类似于 `gt `，是大于等于。


### lt

类似于 `gt `,是小于。

### lte

类似于 `gt`, 是小于等于。

### startswith

判断某个字段的值是否是以某个值开始的。**大小写敏感**。示例代码如下：
```python
    articles = Article.objects.filter(title__startswith='hello')
```
以上代码的意思是提取所有标题以 `hello `字符串开头的文章。将翻译成以下 `SQL `语句：
```sql
    select ... where title like 'hello%';
```

### istartswith

类似于 `startswith `，但是大小写是不敏感的。


### endswith

判断某个字段的值是否以某个值结束。大小写敏感。示例代码如下：
```python
    articles = Article.objects.filter(title__endswith='world')
```
以上代码的意思是提取所有标题以 `world `结尾的文章。将翻译成以下 `SQL `语句：
```sql
    select ... where title like '%world';
```