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

### iendswith

类似于 `endswith `，只不过大小写不敏感。


### range

判断某个 `field `的值是否在给定的区间中。示例代码如下：
```python
    from django.utils.timezone import make_aware
    from datetime import datetime
    
    start_date = make_aware(datetime(year=2018,month=1,day=1))
    end_date = make_aware(datetime(year=2018,month=3,day=29,hour=16))
    articles = Article.objects.filter(pub_date__range=(start_date,end_date))
```
以上代码的意思是提取所有发布时间在 `2018/1/1` 到 `2018/12/12` 之间的文章。将翻译成以下的 `SQL `语句：
```sql
    select ... from article where pub_time between '2018-01-01' and '2018-12-12';
```
需要注意的是，以上提取数据，**不会包含最后一个值**。也就是不会包含 2018/12/12 的文章。而且另外一个重点，因为我们在 `settings.py` 中指定了 `USE_TZ=True` ，并且设置了 `TIME_ZONE='Asia/Shanghai'` ，因此我们在提取数据的时候要使用 `django.utils.timezone.make_aware` 先将 `datetime.datetime` 从 `navie `时间转换为 `aware `时间。 `make_aware `会将指定的时间转换为 `TIME_ZONE `中指定的时区的时间。

### date

针对某些 `date `或者 `datetime `类型的字段。可以指定 `date `的范围。并且这个时间过滤，还可以使用链式调用。示例代码如下：
```python
    articles = Article.objects.filter(pub_date__date=date(2018,3,29))
```
以上代码的意思是查找时间为 `2018/3/29` 这一天发表的所有文章。将翻译成以下的 sql 语句：
```sql
    select ... WHERE DATE(CONVERT_TZ(`front_article`.`pub_date`, 'UTC', 'Asia/Shanghai')) =2018-03-29
```
注意，因为默认情况下 `MySQL `的表中是没有存储时区相关的信息的。因此我们需要下载一些时区表的文件，然后添加到 `Mysql `的配置路径中。如果你用的是 `windows `操作系统。那么在 [http://dev.mysql.com/downloads/timezones.html](http://dev.mysql.com/downloads/timezones.html) 下载 `timezone_2018d_posix.zip - POSIX standard` 。然后将下载下来的所有文件拷贝到 `C:\ProgramData\MySQL\MySQL Server5.7\Data\mysql` 中，如果提示文件名重复，那么选择覆盖即可。
如果用的是 `linux `或者 `mac `系统，那么在命令行中执行以下命令： `mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -D mysql -u root -p` ，然后输入密码，从系统中加载时区文件更新到 `mysql `中。

### year

根据年份进行查找。示例代码如下：
```python
    articles = Article.objects.filter(pub_date__year=2018)
    articles = Article.objects.filter(pub_date__year__gte=2017)
```
以上的代码在翻译成 SQL 语句为如下:
```sql
    select ... where pub_date between '2018-01-01' and '2018-12-31';
    select ... where pub_date >= '2017-01-01';
```
### month

同`year`，根据月份进行查找。

### day

同`year`，根据日期进行查找。

### week_day

`Django 1.11` 新增的查找方式。同 `year `，根据星期几进行查找。1表示星期天，7表示星期六， 2-6 代表的是星期一到星期五。

### time

根据时间进行查找。示例代码如下：
```python
    articles = Article.objects.filter(pub_date__time=datetime.time(12,12,12));
```
以上的代码是获取每一天中12点12分12秒发表的所有文章。
更多的关于时间的过滤，请参考 Django 官方文档:
[https://docs.djangoproject.com/en/2.1/ref/models/querysets/#time](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#time)

### isnull

根据值是否为空进行查找。示例代码如下：
```python
    articles = Article.objects.filter(pub_date__isnull=False)
```
以上的代码的意思是获取所有发布日期不为空的文章。将来翻译成 `SQL `语句如下：
```sql
    select ... where pub_date is not null;
```

### regex和iregex

大小写敏感和大小写不敏感的正则表达式。示例代码如下：
```python
    articles = Article.objects.filter(title__regex=r'^hello')
```
以上代码的意思是提取所有标题以 `hello `字符串开头的文章。将翻译成以下的 `SQL `语句：
```sql
    select ... where title regexp binary '^hello';
```
`iregex `是大小写不敏感的。


# 根据关联的表进行查询

假如现在有两个 `ORM `模型，一个是 `Article `，一个是 `Category `。代码如下：
```python
    class Category(models.Model):
    """文章分类表"""
        name = models.CharField(max_length=100)
        
    class Article(models.Model):
    """文章表"""
        title = models.CharField(max_length=100,null=True)
        category = models.ForeignKey("Category",on_delete=models.CASCADE)
```
