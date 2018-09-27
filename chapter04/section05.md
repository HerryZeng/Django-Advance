# 外键和表关系

## 外键

在`MySQL`中，表有两种引擎，一种是`InnoDB`，另外一种是`myisam`。如果使用的是`InnoDB`引擎，是支持外键约束的。外键的存在使得`ORM`框架在处理表关系的时候异常的强大。因此这里我们首先来介绍下外键在`Django`中的使用。
类定义为`class ForeignKey(to,on_delete,**options)`。第一个参数是引用的哪个模型，第二个参数是在使用外键引用的模型数据被删除了，这个字段该如何处理，比如有`CASCADE`、`SET_NULL`等。这里以一个实际案例来说明。比如有一个`User`和一个`Article`两个模型。一个`User`可以发表多篇文章，一个`Article`只能有一个`Author`，并且通过外键进行引用。示例代码如下：
```python
    class User(models.Model):
    username = models.CharField(max_length=20)
    password = models.CharField(max_length=100)


    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
    
        author = models.ForeignKey("User",on_delete=models.CASCADE)
```
以上使用`ForeignKey`来定义模型之间的关系。即在`article`的实例中可以通过`author`属性来操作对应的`User`模型。这样使用起来非常方便。示例如下：
```python
    from blog.models import User,Article
    
    author = User(username='张三',password='123456')
    author.save()
    
    article = Article(title='abc',content='123')
    article.author=author
    article.save()
```
为什么使用了`ForeignKey`后，就能通过`author`访问到对应的`user`对象呢。因此在底层，`Django`为`Article`表添加一个`属性名_id`的字段（比如**author**的字段名称为**author_id**)，这个字段是一个外键，记录着对应的作者的主键。以后通过`article.author`访问的时候，实际上是先通`author_id`找到对应的数据，然后再提取`User`表中的这条数据，形成一个模型。

如果想要引用另外一个`APP`的模型，那么应该在传递`to`参数的时候，使用`app.model_name`进行指定。以上例为例，如果`User`和`Article`不是在同一个`APP`中，那么在引用的时候示例代码如下：
```python
    # User模型在user这个app中
    class User(models.Model):
        username = models.CharField(max_length=20)
        password = models.CharField(max_length=100)
    
    # Article模型在article这个app中
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        
        author = models.ForeignKey("user.User",on_delete=models.CASCADE)
```
如果模型的外键引用的是本身自己这个模型，那么`to`参数可以为`self` ，或者是这个模型的名字。在论坛开发中，一般评论都可以进行二级评论，即可以针对另外一个评论进行评论，那么在定义模型的时候就需要使用外键来引用自身。示例代码如下：
```python
    class Comment(models.Model):
    content = models.TextField()
    origin_comment = models.ForeignKey('self',on_delete=models.CASCADE,null=True)
    
    # 或者
    # origin_comment = models.ForeignKey('Comment',on_delete=models.CASCADE,null=True)
```
## 外键删除操作

如果一个模型使用了外键。那么在对方那个模型被删掉后，该进行什么样的操作。可以通过 on_delete 来指定。可以指定的类型如下：
1. CASCADE ：级联操作。如果外键对应的那条数据被删除了，那么这条数据也会被删除。
2. PROTECT ：受保护。即只要这条数据引用了外键的那条数据，那么就不能删除外键的那条数据。
3. SET_NULL ：设置为空。如果外键的那条数据被删除了，那么在本条数据上就将这个字段设置为空。如果设置这个选项，前提是要指定这个字段可以为空。
4. SET_DEFAULT ：设置默认值。如果外键的那条数据被删除了，那么本条数据上就将这个字段设置为默认值。如果设置这个选项，前提是要指定这个字段一个默认值。
5. SET() ：如果外键的那条数据被删除了。那么将会获取 SET 函数中的值来作为这个外键的值。 SET 函数可以接收一个可以调用的对象（比如函数或者方法），如果是可以调用的对象，那么会将这个对象调用后的结果作为值返回回去。
6. DO_NOTHING ：不采取任何行为。一切全看数据库级别的约束。以上这些选项只是Django级别的，数据级别依旧是**RESTRICT**！

## 表关系

表之间的关系都是通过外键来进行关联的。而表之间的关系，无非就是三种关系：一对一、一对多（多对一）、多对多等。以下将讨论一下三种关系的应用场景及其实现方式。

### 一对多

1. 应用场景：比如文章和作者之间的关系。一个文章只能由一个作者编写，但是一个作者可以写多篇文章。文章和作者之间的关系就是典型的多对一的关系。
2. 实现方式：一对多或者多对一，都是通过`ForeignKey` 来实现的。还是以文章和作者的案例进行讲解。
```python
    class User(models.Model):
        username = models.CharField(max_length=20)
        password = models.CharField(max_length=100)
        
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        
        author = models.ForeignKey("User",on_delete=models.CASCADE)
```
那么以后在给 `Article `对象指定 `author `，就可以使用以下代码来完成：
```python
    article = Article(title='abc',content='123')
    author = User(username='zhiliao',password='111111')
    # 要先保存到数据库中
    author.save()
    article.author = author
    article.save()
```
并且以后如果想要获取某个用户下所有的文章，可以通过 `article_set `来实现。示例代码如下：
```python
    user = User.objects.first()
    # 获取第一个用户写的所有文章
    articles = user.article_set.all()
    for article in articles:
    print(article)
```

### 一对一

1. 应用场景：比如一个用户表和一个用户信息表。在实际网站中，可能需要保存用户的许多信息，但是有些信息是不经常用的。如果把所有信息都存放到一张表中可能会影响查询效率，因此可以把用户的一些不常用的信息存放到另外一张表中我们叫做 `UserExtension `。但是用户
表 `User `和用户信息表 `UserExtension `就是典型的一对一了。
2. 实现方式： `Django `为一对一提供了一个专门的 `Field `叫做 `OneToOneField `来实现一对一操作。示例代码如下：
```python
    class User(models.Model):
        username = models.CharField(max_length=20)
        password = models.CharField(max_length=100)
        
    class UserExtension(models.Model):
        birthday = models.DateTimeField(null=True)
        school = models.CharField(blank=True,max_length=50)
        user = models.OneToOneField("User", on_delete=models.CASCADE)
```
在 `UserExtension `模型上增加了一个一对一的关系映射。其实底层是在 `UserExtension `这个表上增加了一个 `user_id `，来和 `user `表进行关联，并且这个外键数据在表中必须是唯一的，来保证一对一。


### 对多对

1. 应用场景：比如文章和标签的关系。一篇文章可以有多个标签，一个标签可以被多个文章所引用。因此标签和文章的关系是典型的多对多的关系。
2. 实现方式： `Django `为这种多对多的实现提供了专门的 `Field `。叫做 `ManyToManyField `。还是拿文章和标签为例进行讲解。示例代码如下：
```python
    class Tag(models.Model):
        name = models.CharField(max_length=50)

    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        tags = models.ManyToManyField("Tag",related_name="articles")
```
在数据库层面，实际上 `Django `是为这种多对多的关系建立了一个中间表。这个中间表分别定义了两个外键，引用到 `article `和 `tag `两张表的主键。


## related_name和related_query_name

### related_name

还是以 `User `和 `Article `为例来进行说明。如果一个 `article `想要访问对应的作者，那么可以通过 `author `来进行访问。但是如果有一个 `user `对象，想要通过这个 `user `对象获取所有的文章，该如何做呢？这时候可以通过 `user.article_set` 来访问，这个名字的规律是 `模型名字小写_set` 。示例代码如下：
```python
    user = User.objects.get(name='张三')
    user.article_set.all()
```
如果不想使用 `模型名字小写_set` 的方式，想要使用其他的名字，那么可以在定义模型的时候指定 `related_name `。示例代码如下：
```python
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        # 传递related_name参数，以后在方向引用的时候使用articles进行访问
        author = models.ForeignKey("User",on_delete=models.SET_NULL,null=True,related_name='articles')
```
以后在方向引用的时候。使用 `articles `可以访问到这个作者的文章模型。示例代码如下：
```python
    user = User.objects.get(name='张三')
    user.articles.all()
```
如果不想使用反向引用，那么可以指定 `related_name='+'` 。示例代码如下：
```python
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        # 传递related_name参数，以后在方向引用的时候使用articles进行访问
        author = models.ForeignKey("User",on_delete=models.SET_NULL,null=True,related_name='+')
```
以后将不能通过 `user.article_set` 来访问文章模型了。

### related_query_name

在查找数据的时候，可以使用 `filter `进行过滤。使用 `filter `过滤的时候，不仅仅可以指定本模型上的某个属性要满足什么条件，还可以指定相关联的模型满足什么属性。比如现在想要获取写过标题为 `abc `的所有用户，那么可以这样写：
```python
    users = User.objects.filter(article__title='abc')
```
如果你设置了 `related_name `为 `articles `，因为反转的过滤器的名字将使用 `related_name `的名字，那么上例代码将改成如下：
```python
    users = User.objects.filter(articles__title='abc')
```
可以通过 `related_query_name `将查询的反转名字修改成其他的名字。比如 `article `。示例代码如下：
```python
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        # 传递related_name参数，以后在方向引用的时候使用articles进行访问
        author = models.ForeignKey("User",on_delete=models.SET_NULL,null=True,related_name='articles',related_query_name='article')
```