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