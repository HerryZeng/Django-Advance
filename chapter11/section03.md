## 权限

###登录

在使用`authenticate`进行验证后，如果验证通过了。那么会返回一个`user`对象，拿到`user`对象后，可以使用`django.contrib.auth.login`进行登录。示例代码如下：
```python
    user = authenticate(username=username, password=password)
    if user is not None:
    if user.is_active:
        login(request, user)
```

### 注销

注销，或者说退出登录。我们可以通过`django.contrib.auth.logout`来实现。他会清理掉这个用户的`session`数据。


### 登录限制

有时候，某个视图函数是需要经过登录后才能访问的。那么我们可以通过`django.contrib.auth.decorators.login_required`装饰器来实现。示例代码如下:
```python
    from django.contrib.auth.decorators import login_required

    # 在验证失败后，会跳转到/accounts/login/这个url页面
    @login_required(login_url='/accounts/login/')
    def my_view(request):
        pass
```

### 权限

`Django`中内置了权限的功能。他的权限都是针对表或者说是模型级别的。比如对某个模型上的数据是否可以进行增删改查操作。他不能针对数据级别的，比如对某个表中的某条数据能否进行增删改查操作（如果要实现数据级别的，考虑使用django-guardian）。创建完一个模型后，针对这个模型默认就有三种权限，分别是增/删/改/。可以在执行完`migrate`命令后，查看数据库中的`auth_permission`表中的所有权限。
![](../images/chapter11/001.png)
其中的`codename`表示的是权限的名字。`name`表示的是这个权限的作用。

### 通过定义模型添加权限

如果我们想要增加新的权限，比如查看某个模型的权限，那么我们可以在定义模型的时候在`Meta`中定义好。示例代码如下：
```python
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        author = models.ForeignKey(get_user_model(),on_delete=models.CASCADE)
    
        class Meta:
            permissions = (
                ('view_article','can view article'),
            )
```

### 通过代码添加权限

权限都是`django.contrib.auth.Permission`的实例。这个模型包含三个字段，`name`、`codename`以及`content_type`，其中的`content_type`表示这个`permission`是属于哪个`app`下的哪个`models`。用`Permission`模型创建权限的代码如下：
```python
    from django.contrib.auth.models import Permission,ContentType
    from .models import Article
    
    content_type = ContentType.objects.get_for_model(Article)
    permission = Permission.objects.create(name='可以编辑的权限',codename='edit_article',content_type=content_type)
```

### 用户与权限管理

权限本身只是一个数据，必须和用户进行绑定，才能起到作用。`User`模型和权限之间的管理，可以通过以下几种方式来管理：
1. `myuser.user_permissions.set(permission_list)`：直接给定一个权限的列表。
2. `myuser.user_permissions.add(permission,permission,...)`：一个个添加权限。
3. `myuser.user_permissions.remove(permission,permission,...)`：一个个删除权限。
4. `myuser.user_permissions.clear()`：清除权限。
5. `myuser.has_perm('<app_name>.<codename>')`：判断是否拥有某个权限。权限参数是一个字符串，格式是`app_name.codename`。
6. `myuser.get_all_permissons()`：获取所有的权限。

### 权限限定装饰器

使用`django.contrib.auth.decorators.permission_required`可以非常方便的检查用户是否拥有这个权限，如果拥有，那么就可以进入到指定的视图函数中，如果不拥有，那么就会报一个400错误。示例代码如下：
```python
    from django.contrib.auth.decorators import permission_required

    @permission_required('front.view_article')
    def my_view(request):
        ...
```

## 分组

权限有很多，一个模型就有最少三个权限，如果一些用户拥有相同的权限，那么每次都要重复添加。这时候分组就可以帮我们解决这种问题了，我们可以把一些权限归类，然后添加到某个分组中，之后再把和把需要赋予这些权限的用户添加到这个分组中，就比较好管理了。分组我们使用的是`django.contrib.auth.models.Group`模型， 每个用户组拥有`id`和`name`两个字段，该模型在数据库被映射为`auth_group`数据表。

### 分组操作

1. `Group.object.create(group_name)`：创建分组。
2. `group.permissions`：某个分组上的权限。多对多的关系。
    + `group.permissions.add`：添加权限。
    + `group.permissions.remove`：移除权限。
    + `group.permissions.clear`：清除所有权限。
    + `user.get_group_permissions()`：获取用户所属组的权限。
3. `user.groups`：某个用户上的所有分组。多对多的关系。

### 在模板中使用权限

在`settings.TEMPLATES.OPTIONS.context_processors`下，因为添加了`django.contrib.auth.context_processors.auth`上下文处理器，因此在模板中可以直接通过`perms`来获取用户的所有权限。示例代码如下：
