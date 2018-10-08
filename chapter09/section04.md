## SQL注入

所谓`SQL`注入，就是通过把`SQL`命令插入到表单中或页面请求的查询字符串中，最终达到欺骗服务器执行恶意的`SQL`命令。具体来说，它是利用现有应用程序，将（恶意的）`SQL`命令注入到后台数据库引擎执行的能力，它可以通过在Web表单中输入（恶意）`SQL`语句得到一个存在安全漏洞的网站上的数据库，而不是按照设计者意图去执行`SQL`语句。 比如先前的很多影视网站泄露VIP会员密码大多就是通过WEB表单递交查询字符暴出的。

### 场景

比如现在数据库中有一个`front_user`表，表结构如下：
```python
    class User(models.Model):
        telephone = models.CharField(max_length=11)
        username = models.CharField(max_length=100)
        password = models.CharField(max_length=100)
```
然后我们使用原生sql语句实现以下需求：
1. 实现一个根据用户id获取用户详情的视图。示例代码如下：
```python
    def index(request):
         user_id = request.GET.get('user_id')
         cursor = connection.cursor()
         cursor.execute("select id,username from front_user where id=%s" % user_id)
         rows = cursor.fetchall()
         for row in rows:
             print(row)
         return HttpResponse('success')
```
这样表面上看起来没有问题。但是如果用户传的`user_id`是等于`1 or 1=1`，那么以上拼接后的sql语句为：
```sql
    select id,username from front_user where id=1 or 1=1
```
以上`sql`语句的条件是`id=1 or 1=1`，只要`id=1`或者是`1=1`两个有一个成立，那么整个条件就成立。毫无疑问`1=1`是肯定成立的。因此执行完以上`sql`语句后，会将`front_user`表中所有的数据都提取出来。
2. 实现一个根据用户的`username`提取用户的视图。示例代码如下：
```python
    def index(request):
        username = request.GET.get('username')
        cursor = connection.cursor()
        cursor.execute("select id,username from front_user where username='%s'" % username)
        rows = cursor.fetchall()
        for row in rows:
            print(row)
        return HttpResponse('success')
```
这样表面上看起来也没有问题。但是如果用户传的`username`是`zhiliao' or '1=1`，那么以上拼接后的sql语句为：
```sql
    select id,username from front_user where username='zhiliao' or '1=1'
```
以上`sql`语句的条件是`username='zhiliao'`或者是一个字符串，毫无疑问，字符串的判断是肯定成立的。因此会将`front_user`表中所有的数据都提取出来。

### sql注入防御

以上便是`sql`注入的原理。他通过传递一些恶意的参数来破坏原有的`sql`语句以便达到自己的目的。当然`sql`注入远远没有这么简单，我们现在讲到的只是冰山一角。那么如何防御`sql`注入呢？归类起来主要有以下几点：
1. 永远不要信任用户的输入。对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和 双"-"进行转换等。
2. 永远不要使用动态拼装`sql`，可以使用参数化的`sql`或者直接使用存储过程进行数据查询存取。比如：
```python
    def index(request):
        user_id = "1 or 1=1"
        cursor = connection.cursor()
        cursor.execute("select id,username from front_user where id=%s",(user_id,))
        rows = cursor.fetchall()
        for row in rows:
            print(row)
        return HttpResponse('success')
```