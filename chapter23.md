## 聚合内容 RSS/Atom

Django提供了一个高层次的聚合内容框架，让我们创建RSS/Atom变得简单，你需要做的只是编写一个简单的Python类。

---

### 一、范例

要创建一个`feed`，只需要编写一个`Feed`类，然后设置一条指向`Feed`实例的`URLconf`就可以了，非常简单，下面是一个示例，演示了某站点的最近五条新闻记录：
```python
from django.contrib.syndication.views import Feed
from django.urls import reverse
from policebeat.models import NewsItem

class LatestEntriesFeed(Feed):
    title = "Police beat site news"
    link = "/sitenews/"
    description = "Updates on changes and additions to police beat central."

    def items(self):
        return NewsItem.objects.order_by('-pub_date')[:5]

    def item_title(self, item):
        return item.title

    def item_description(self, item):
        return item.description

    # item_link is only needed if NewsItem has no get_absolute_url method.
    def item_link(self, item):
        return reverse('news-item', args=[item.pk])
```
要设置链接这个`feed`的`URL`，只需要将这个`Feed`类的实例，作为参数，加入`URLconf`，如下所示：
```python
from django.conf.urls import url
from myproject.feeds import LatestEntriesFeed

urlpatterns = [
    # ...
    url(r'^latest/feed/$', LatestEntriesFeed()),
    # ...
]
```
注意:
    + 新建的`Feed`类继承于`django.contrib.syndication.views.Feed`。
    + `title`、`link`和`description`属性分别对应标准RSS的`<title>`、`<link>`和`<description>`元素。
    + `items()`方法简单地返回此Feed需要包含的对象，列表形式。
    + 如果你要创建一个`Atom feed`，而不是`RSS feed`，使用`subtitle`属性替代`description`。

还有一件事要做。在一个 `RSS feed`中，每一个`<item>`都有一个`<title>, <link> 和<description>`， 我们需要告诉框架往这些对象里放入哪些数据。
    + 对于`<title>`和`<description>`，Django将尝试调用Feed类中的`item_title()`和`item_description()`方法。 这两个方法都会被传入一个参数：`item`，也就是对象自己。
    对于`<link>`，Django首先会尝试调用`item_link()`方法，如果该方法不存在，则使用对象的ORM模型中定义的`get_absolute_url()`方法。
    
    
---

### 指定feed类型

默认情况下，使用RSS 2.0类型，如果要指定类型，在Feed类中添加feed_type属性，如下所示:
```python
from django.utils.feedgenerator import Atom1Feed

class MyFeed(Feed):
    feed_type = Atom1Feed
```
目前可用的类型有下面三种：
    + django.utils.feedgenerator.Rss201rev2Feed (RSS 2.01. Default.)
    + django.utils.feedgenerator.RssUserland091Feed (RSS 0.91.)
    + django.utils.feedgenerator.Atom1Feed (Atom 1.0.)
    

---

### 同时发布Atom和RSS feeds

要同时发布这两者，很简单，为你的Feed类创建一个子类，并且将其feed_type设置为你需要的类型，最后添加一条URLconf就可以了，如下所示：
```python
from policebeat.models import NewsItem
from django.utils.feedgenerator import Atom1Feed

class RssSiteNewsFeed(Feed):
    title = "Police beat site news"
    link = "/sitenews/"
    description = "Updates on changes and additions to police beat central."

    def items(self):
        return NewsItem.objects.order_by('-pub_date')[:5]

# 增加下面的子类
class AtomSiteNewsFeed(RssSiteNewsFeed):
    feed_type = Atom1Feed # 修改类型
    subtitle = RssSiteNewsFeed.description
```
增加路由：
```python
from django.conf.urls import url
from myproject.feeds import RssSiteNewsFeed, AtomSiteNewsFeed

urlpatterns = [
    # ...
    url(r'^sitenews/rss/$', RssSiteNewsFeed()),
    url(r'^sitenews/atom/$', AtomSiteNewsFeed()),
    # ...
]
```
