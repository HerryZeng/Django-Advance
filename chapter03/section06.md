# 加载静态文件

在一个网页中，不仅仅只有一个`html`骨架，还需要`css`样式文件，`js`执行文件以入一些图片等。因此在`DTL`中加载静态文件是一个必须要解决的问题。在`DTL`中，使用`static`标签来加载静态文件。要使用`static`标签，首先需要`{% load static %}`。加载静态文件的步骤如下：
1. 首先确保`django.contrib.staticfiles`已经添加到`settings.INSTALLED_APPS`中。
2. 确保在`settings.py`中设置了`STATIC_URL`。
3. 在已安装了的`APP`下创建目录`static`，然后再在这个`static`下创建一个当前`APP`的名a字的目录，再把静态文件放在这个目录下。如你的`APP`叫做`book`，有一个静态文件叫做`abc.jgp`，那么路径为`book/static/book/abc.jgp`。
4. 如果有些静态文件是不和任何`APP`挂钩的，那么可以在`settings.py`中添`STATICFILES_DIRS`,以后`DTL`就会在这个列表的路径中查找静态文件。比如可以设置为：
```python
    STATICFILES_DIRS = [
        os.path.join(BASE_DIR,'static'),
    ]
```
5. 在模板中使用`load` 标签加载`static`标签，比如要加载在项目的`static`目录下的`style.css`文件。示例代码如下：
```html
    {% load static %}
    <link ref="stylesheet" href="{% static 'style.css' %}"
```
6. 如果不想每次在模板中加载静态文件都使用`load`加载`static`标签，那么可以在`settings.py`中的`TEMPLATES/OPTIONS`添加`builtins：['django.templatetags.static']`，这样以后在模板中变可以直接使用`static`标签，而不用手动的`load`了。
7. 如果没有在`settings.INSTALLED_APPS`中添加`django.contrib.staticfiles`。那么我们就南非要手动的将请求静态文件的`url`与静态文件的路径进行映射了。示例代码如下：
```python
    from django.conf import settings
    from django.conf.urls.static import static
    
    urlpatterns = [
        # 其他的url映射
    ] + static(settings.STATIC_URL,document_root=settings.STATIC_ROOT)
```