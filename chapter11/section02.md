## User模型

`User`模型是这个框架的核心部分。他的完整的路径是在`django.contrib.auth.models.User`。以下对这个`User`对象做一个简单了解：

### 字段 

内置的`User`模型拥有以下的字段：
1. `username`： 用户名。150个字符以内。可以包含数字和英文字符，以及_、@、+、.和-字符。**不能为空，且必须唯一**！
2. `first_name`：歪果仁的`first_name`，在30个字符以内。**可以为空**。
3. `last_name`：歪果仁的`last_name`，在150个字符以内。**可以为空**。
4. `email`：邮箱。可以为空。
5. `password`：密码。经过哈希过后的密码。
6. `groups`：分组。一个用户可以属于多个分组，一个分组可以拥有多个用户。groups这个字段是跟Group的一个多对多的关系。
7. `user_permissions`：权限。一个用户可以拥有多个权限，一个权限可以被多个用户所有用。和Permission属于一种多对多的关系。
8. `is_staff`：是否可以进入到admin的站点。代表是否是员工。
9. `is_active`：是否是可用的。对于一些想要删除账号的数据，我们设置这个值为False就可以了，而不是真正的从数据库中删除。
10. `is_superuser`：是否是超级管理员。如果是超级管理员，那么拥有整个网站的所有权限。
11. `last_login`：上次登录的时间。
12. `date_joined`：账号创建的时间。

## User模型的基本用法

### 创建用户

通过`create_user`方法可以快速的创建用户。这个方法必须要传递`username`、`email`、`password`。示例代码如下：
```python
    from django.contrib.auth.models import User
    user = User.objects.create_user('zhiliao','hynever@zhiliao.com','111111')
    # 此时user对象已经存储到数据库中了。当然你还可以继续使用user对象进行一些修改
    user.last_name = 'abc'
    user.save()
```

### 创建超级用户

创建超级用户有两种方式。第一种是使用代码的方式。用代码创建超级用户跟创建普通用户非常的类似，只不过是使用`create_superuser`。示例代码如下：
```python
    from django.contrib.auth.models import User
    User.objects.create_superuser('admin','admin@163.com','111111')
```
也可以通过命令行的方式。命令如下：
```bash
    python manage.py createsuperuser
```
后面就会提示你输入用户名、邮箱以及密码。

### 修改密码

因为密码是需要经过加密后才能存储进去的。所以如果想要修改密码，不能直接修改`password`字段，而需要通过调用`set_password`来达到修改密码的目的。示例代码如下：
```python
    from django.contrib.auth.models import User
    user = User.objects.get(pk=1)
    user.set_password('新的密码')
    user.save()
```

### 登录验证

`Django`的验证系统已经帮我们实现了登录验证的功能。通过`django.contrib.auth.authenticate`即可实现。这个方法只能通过`username`和`password`来进行验证。示例代码如下：
```python
    from django.contrib.auth import authenticate
    user = authenticate(username='zhiliao', password='111111')
    # 如果验证通过了，那么就会返回一个user对象。
    if user is not None:
        # 执行验证通过后的代码
    else:
        # 执行验证没有通过的代码。
```

## 扩展用户模型

`Django`内置的`User`模型虽然已经足够强大了。但是有时候还是不能满足我们的需求。比如在验证用户登录的时候，他用的是用户名作为验证，而我们有时候需要通过手机号码或者邮箱来进行验证。还有比如我们想要增加一些新的字段。那么这时候我们就需要扩展用户模型了。扩展用户模型有多种方式。这里我们来一一讨论下。

### 1. 设置Proxy模型

如果你对`Django`提供的字段，以及验证的方法都比较满意，没有什么需要改的。但是只是需要在他原有的基础之上增加一些操作的方法。那么建议使用这种方式。示例代码如下：
```python
    class Person(User):
        class Meta:
            proxy = True
        @classmethod
        def get_blacklist(self):
            return self.objects.filter(is_active=False)
```
在以上，我们定义了一个`Person`类，让他继承自`User`，并且在`Meta`中设置`proxy=True`，说明这个只是`User`的一个代理模型。他并不会影响原来`User`模型在数据库中表的结构。以后如果你想方便的获取所有黑名单的人，那么你就可以通过`Person.get_blacklist()`就可以获取到。并且`User.objects.all()`和`Person.objects.all()`其实是等价的。因为他们都是从`User`这个模型中获取所有的数据。

### 2. 一对一外键

如果你对用户验证方法`authenticate`没有其他要求，就是使用`username`和`password`即可完成。但是想要在原来模型的基础之上添加新的字段，那么可以使用一对一外键的方式。示例代码如下：
```python
    from django.contrib.auth.models import User
    from django.db import models
    from django.dispatch import receiver
    from django.db.models.signals import post_save
    
    class UserExtension(models.Model):
        user = models.OneToOneField(User,on_delete=models.CASCADE,related_name='extension')
        birthday = models.DateField(null=True,blank=True)
        school = models.CharField(max_length=100)
    
    
    @receiver(post_save,sender=User)
    def create_user_extension(sender,instance,created,**kwargs):
        if created:
            UserExtension.objects.create(user=instance)
        else:
            pass
```
以上定义一个`UserExtension`的模型，并且让她和`User`模型进行一对一的绑定，以后我们新增的字段，就添加到`UserExtension`上。并且还写了一个接受保存模型的信号处理方法，只要是`User`调用了`save`方法，那么就会创建一个`UserExtension`和`User`进行绑定。

### 3. 继承自AbstractUser

对于`authenticate`不满意，并且不想要修改原来User对象上的一些字段，但是想要增加一些字段，那么这时候可以直接继承自`django.contrib.auth.models.AbstractUser`，其实这个类也是`django.contrib.auth.models.User`的父类。比如我们想要在原来`User`模型的基础之上添加一个`telephone`和`school`字段。示例代码如下：
```python
    from django.contrib.auth.models import AbstractUser
    class User(AbstractUser):
        telephone = models.CharField(max_length=11,unique=True)
        school = models.CharField(max_length=100)
    
        # 指定telephone作为USERNAME_FIELD，以后使用authenticate
        # 函数验证的时候，就可以根据telephone来验证
        # 而不是原来的username
        USERNAME_FIELD = 'telephone'
        REQUIRED_FIELDS = []
    
        # 重新定义Manager对象，在创建user的时候使用telephone和
        # password，而不是使用username和password
        objects = UserManager()
```
然后再在`settings`中配置好`AUTH_USER_MODEL=youapp.User`。
**这种方式因为破坏了原来`User`模型的表结构，所以必须要在第一次`migrate`前就先定义好。**

### 4. 继承自AbstractBaseUser模型

如果你想修改默认的验证方式，并且对于原来`User`模型上的一些字段不想要，那么可以自定义一个模型，然后继承自`AbstractBaseUser`，再添加你想要的字段。这种方式会比较麻烦，最好是确定自己对`Django`比较了解才推荐使用。步骤如下：
1. 创建模型。示例代码如下：
```python
    class User(AbstractBaseUser,PermissionsMixin):
        email = models.EmailField(unique=True)
        username = models.CharField(max_length=150)
        telephone = models.CharField(max_length=11,unique=True)
        is_active = models.BooleanField(default=True)
        
        USERNAME_FIELD = 'telephone'
        REQUIRED_FIELDS = []
        
        objects = UserManager()
        
        def get_full_name(self):
            return self.username
        
        def get_short_name(self):
            return self.username
```
    + 其中`password`和`last_login`是在`AbstractBaseUser`中已经添加好了的，我们直接继承就可以了。然后我们再添加我们想要的字段。比如`email`、`username`、`telephone`等。这样就可以实现自己想要的字段了。但是因为我们重写了`User`，所以应该尽可能的模拟User模型：
    + `USERNAME_FIELD`：用来描述`User`模型名字字段的字符串，作为唯一的标识。如果没有修改，那么会使用USERNAME来作为唯一字段。
    + `REQUIRED_FIELDS`：一个字段名列表，用于当通过`createsuperuser`管理命令创建一个用户时的提示。
    + `is_active`：一个布尔值，用于标识用户当前是否可用。
    + `get_full_name()`：获取完整的名字。
    + `get_short_name()`：一个比较简短的用户名。
    
2. 重新定义`UserManager`：我们还需要定义自己的`UserManager`，因为默认的`UserManager`在创建用户的时候使用的是`username`和`password`，那么我们要替换成`telephone`。示例代码如下：
```python
    class UserManager(BaseUserManager):
        use_in_migrations = True
        
        def _create_user(self,telephone,password,**extra_fields):
            if not telephone:
                raise ValueError("请填入手机号码！")
            user = self.model(telephone=telephone,*extra_fields)
            user.set_password(password)
            user.save()
            return user
        
        def create_user(self,telephone,password,**extra_fields):
            extra_fields.setdefault('is_superuser',False)
            return self._create_user
```
3. 在创建了新的`User`模型后，还需要在`settings`中配置好。配置`AUTH_USER_MODEL='appname.User'`。
4. 如何使用这个自定义的模型：比如以后我们有一个`Article`模型，需要通过外键引用这个`User`模型，那么可以通过以下两种方式引用。第一种就是直接将User导入到当前文件中。示例代码如下：
```python
    from django.db import models
    from myauth.models import User
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        author = models.ForeignKey(User, on_delete=models.CASCADE)
```
这种方式是可以行得通的。但是为了更好的使用性，建议还是将`User`抽象出来，使用`settings.AUTH_USER_MODEL`来表示。示例代码如下：
```python
    from django.db import models
    from django.conf import settings
    
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
```
**这种方式因为破坏了原来`User`模型的表结构，所以必须要在第一次`migrate`前就先定义好。**