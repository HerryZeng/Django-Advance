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