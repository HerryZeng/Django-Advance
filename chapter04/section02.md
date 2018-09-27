# 操作数据库

## Django配置连接数据库

在操作数据库之前，首先要连接数据库。这我们以配置`MySQL`为例来讲解。`Django`连接数据库，不需要单独的创建一个连接对象。只需要在`settings.py`文件中做好数据库相关的配置就可以了。示例如下：
```python
    DATABASES ={
        'default':{
            # 数据库引擎（是mysql还是oracle等）
            'ENGINE': 'django.db.backends.mysql',
            # 数据库的名字
            'NAME': 'abc',
            # 连接mysql数据库的用户名
            'USER': 'root',
            # 连接mysql的数据的密码
            'PASSWORD': '12345678',
            # mysql数据库的主机地址
            'HOST': '127.0.0.1',
            # mysql数据库的端口号
            'PORT':'3306',
        }
    }
```

## 在Django中操作数据库

在`Django`中操作数据库有两种方式。第一种方式是使用原生`sql`语句操作；第二种就是使用`ORM`模型来操作。这节首先来讲第一种。
在`Django`中使用原生`sql`语句操作其实就是使用`python db api`的接口来操作。如果你的`mysql`驱动使用的是`pymysql`，那么你就是使用`pymysql`来操作，只不过`Django`将数据库连接的这一部分封装好了，我们只要在`settings.py`中配置好的数据库连接信息直接使用`Django`封装好的接口就可以操作了。示例如下 ：
```python
    # 使用django封装好的connection对象，会自动读取settings.py中的数据库配置信息
    from django.db import connection
    
    # 获取游标对象
    cursor = connection.cursor()
    # 拿到游标对象后执行sql语句
    cursor.execute("select * from book")
    # 获取所有的数据
    rows = cursor.fetchall()
    # 遍历查询到的数据
    for row in rows:
        print(row)
```
以上`execute`以及`fetchall`方法都是`Python DB API`规范中定义好的。任何使用`Python`来操作`MySQL`的驱动程序者应该遵循这个规范。所以不管是用`pymysql`或是`mysqlclient`或`mysqldb`,他们的接口都是一样的。更多的规范请参考：[https://www.python.org/dev/peps/pep-0249/#cursor-methods](https://www.python.org/dev/peps/pep-0249/#cursor-methods)

## Python DB API下规范下cursor对象常用接口

1. `description`:如果`cursor`执行了查询的`sql`代码。那么读取`cursor.description`属性的时候，将返回一个列表，这个列表中装的是元组，元组中装的分别是`(name,type_code,display_size,internal_size,precision,scale,null_ok)`，其中`name`代表的是查找出来的数据的字段名称，其他参数暂时用处不在。
2. `rowcount`:代表的是在执行了`sql`语句后受影响的行数。
3. `close`: 关闭游标，关闭游标之后再也不能使用了，否则会抛出异常。
4. `execute(sql[, parameters])`：执行某个`sql`语句。如果在执行`sql`语句的时候还需要传递参数，那么可以传给`parameters`参数，示例代码如下：
```sql
    cursor.execute("select * from article where id=%s",(1,))
```
5. `fetchone`: 在执行了查询操作以后，获取第一条数据。
6. `fetchmany(size)`: 在执行查询操作以后，获取多条数据，具体是多少条要看传的`size`参数。如果不传`size`参数，那么默认是获取第一条数据。
7. `fetchall`：获取所有满足`sql`语句的数据。
