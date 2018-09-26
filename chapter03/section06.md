# 加载静态文件

在一个网页中，不仅仅只有一个`html`骨架，还需要`css`样式文件，`js`执行文件以入一些图片等。因此在`DTL`中加载静态文件是一个必须要解决的问题。在`DTL`中，使用`static`标签来加载静态文件。要使用`static`标签，首先需要`{% load static %}`。加载静态文件的步骤如下：
1. 首先确保`django.contrib.staticfiles`已经添加到`settings.INSTALLED_APPS`中。
2. 确保在`settings.py`中设置了`STATIC_URL`。
3. 在已安装了的`APP`下创建目录`static`，然后再在这个`static`下创建一个当前`APP`的名a字的目录，再把静态文件放在这个目录下。如你的`APP`叫做`book`，有一个静态文件叫做`abc.jgp`，那么路径为`book/static/book/abc.jgp`。