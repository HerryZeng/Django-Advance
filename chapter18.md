## 分页 Paginator

---

分页功能是几乎所有的网站都需要提供的功能，当你要展示的条目比较多时，必须进行分页，不便能减小数据库读取数据压力，也有利于用户浏览。
Django又很贴心的为我们提供了一个Paginator分页工具，但不幸的是，这个工具功能差了点，不好添加CSS样式，所以前端的展示效果比较丑。如果有能力，自己编写一个分页器，然后提交给Django官方。

但不管怎样，当前的Paginator分页器，还是值得学一下用一下的。
---

### 一、实例展示

向Paginator提供包含一些对象的列表，以及你想第页显示几条，比如每页5条、10条、20条、100条等，它就会为你提供访问的一系列API方法。示例如下：
```python
>>> from django.core.paginator import Paginator
>>> objects = ['john', 'paul', 'george', 'ringo']
>>> p = Paginator(objects, 2)  # 对objects进行分页，虽然objects只是个字符串列表，但没关系，一样用。每页显示2条。

>>> p.count   # 对象个数
4
>>> p.num_pages  # 总共几页
2
>>> type(p.page_range)  # `<type 'rangeiterator'>` in Python 2.
<class 'range_iterator'>
>>> p.page_range  # 分页范围
range(1, 3)

>>> page1 = p.page(1) # 获取第一页
>>> page1
<Page 1 of 2>
>>> page1.object_list # 获取第一页的对象
['john', 'paul']

>>> page2 = p.page(2)
>>> page2.object_list
['george', 'ringo']
>>> page2.has_next()  # 判断是否有下一页
False
>>> page2.has_previous()# 判断是否有上一页
True
>>> page2.has_other_pages() # 判断是否有其它页
True
>>> page2.next_page_number() # 获取下一页的页码
Traceback (most recent call last):
...
EmptyPage: That page contains no results
>>> page2.previous_page_number() # 获取上一页的页码
1
>>> page2.start_index() # 从1开始计数的当前页的第一个对象
3
>>> page2.end_index() # 从1开始计数的当前页最后1个对象
4

>>> p.page(0)  # 访问不存在的页面
Traceback (most recent call last):
...
EmptyPage: That page number is less than 1
>>> p.page(3) # 访问不存在的页面
Traceback (most recent call last):
...
EmptyPage: That page contains no results
```
简单来说，使用`Paginator`分四步走：
    + 使用任何方法，获取要展示的对象列表`QuerySet`
    + 将列表和每页个数传递给`Paginator`，返回一个分页对象
    + 调用该对象的各种方法，获取各种分页信息
    + 在`HTML`模板中，使用上面的分页信息构建分页栏。
    
---

### 二、在视图中使用`Paginator`


