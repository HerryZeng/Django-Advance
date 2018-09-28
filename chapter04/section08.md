# QuerySet API

我们通常做查询操作的时候，都是通过 `模型名字.objects` 的方式进行操作。其实 `模型名字.objects` 是一个 `django.db.models.manager.Manager` 对象，而 `Manager` 这个类是一个“空壳”的类，他本身是没有任何的属性和方法的。他的方法全部都是通过 `Python`动态添加的方式，从 `QuerySet`类中拷贝过来的。示例图如下：
![QuerySet API](../images/chapter04/QuerySet_API.png)
所以我们如果想要学习 `ORM`模型的查找操作，必须首先要学会 `QuerySet`上的一些 API 的使用。

## 返回新的QuerySet方法

