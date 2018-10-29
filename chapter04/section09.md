## ORM模型迁移

### 迁移命令：

1. `makemigrations`：将模型生成迁移脚本。模型所在的`app`，必须放在`settings.py`中的`INSTALLED_APPS`中。这个命令有以下几个常用选项：
    + `app_label`：后面可以跟一个或者多个`app`，那么就只会针对这几个`app`生成迁移脚本。如果没有任何的`app_label`，那么会检查`INSTALLED_APPS`中所有的`app`下的模型，针对每一个`app`都生成响应的迁移脚本。
    + `--name`：给这个迁移脚本指定一个名字。
    + `--empty`：生成一个空的迁移脚本。如果你想写自己的迁移脚本，可以使用这个命令来实现一个空的文件，然后自己再在文件中写迁移脚本。
2. `migrate`：将新生成的迁移脚本。映射到数据库中。创建新的表或者修改表的结构。以下一些常用的选项：
    + `app_label`：将某个`app`下的迁移脚本映射到数据库中。如果没有指定，那么会将所有在`INSTALLED_APPS`中的`app`下的模型都映射到数据库中。
    + `app_label migrationname`：将某个`app`下指定名字的`migration`文件映射到数据库中。
    + `--fake`：可以将指定的迁移脚本名字添加到数据库中。但是并不会把迁移脚本转换为`SQL`语句，修改数据库中的表。
    + `--fake-initial`：将第一次生成的迁移文件版本号记录在数据库中。但并不会真正的执行迁移脚本。
3. `showmigrations`：查看某个`app`下的迁移文件。如果后面没有`app`，那么将查看`INSTALLED_APPS`中所有的迁移文件。
4. `sqlmigrate`：查看某个迁移文件在映射到数据库中的时候，转换的`SQL`语句。

### migrations中的迁移版本和数据库中的迁移版本对不上怎么办？

1. 找到哪里不一致，然后使用`python manage.py --fake [版本名字]`，将这个版本标记为已经映射。
2. 删除指定`app`下`migrations`和数据库表`django_migrations`中和这个`app`相关的版本号，然后将模型中的字段和数据库中的字段保持一致，再使用命令`python manage.py makemigrations`重新生成一个初始化的迁移脚本，之后再使用命令`python manage.py makemigrations --fake-initial`来将这个初始化的迁移脚本标记为已经映射。以后再修改就没有问题了。

更多关于迁移脚本的。请查看官方文档：
[https://docs.djangoproject.com/en/2.1/topics/migrations/](https://docs.djangoproject.com/en/2.1/topics/migrations/)