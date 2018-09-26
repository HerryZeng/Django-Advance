# 模板结构优化

## 引入模板

有时候一些代码是在许多模板中都用到的。如果我们每次都重复的去复制代码肯定不符合项目的规范。一般我们可以把这些重复性的代码抽取出来，就类似于`Python`中的函数一样，以后想要使用这些代码的时候，就通过`include`包含进来。这个标签就是`include`。示例代码如下：
```html
    # header.html
    <p>我是header<p>
    
    # footer.html
    <p>我是footer</p>
    
    # main.html
    {% include 'header.html' %}
    <p>我是main内容<p>
    {% include 'footer.html' %}
```
`include`标签寻找路径的方式。也是跟`render`渲染模板函数一样。
默认`include`标签包含模板，会自动的使用主模板中的上下文，也即可以自动的使用主模板中的变量。如果想传入一些其他的参数，那么可以使用`with`语句。示例如下：
```html
    # header.html
    <p>用户名：{{ username }}</p>
    
    # main.html
    {% include "header.html" with username='zhangsan' %}
```

## 模板继承

在前端页面开发中，有些代码是需要重复使用的。这种情况下可以使用`include`标签来实现。也可以使用另外一些比较强大的方式来实现，那就是模板继承。模板继承类似于`Python`中的类，在父类中可以先定义好一些变量和方法，然后在子类中实现。模板继承也可以在父模板中先定义好一些子模板需要用到的代码，然后子模板直接继承就好了。并且因为子模板肯定有自己的不同代码，因此可以在父模板中定义一个`block`接口，然后子模板再去实现。示例如下：
```html
    {% load static %}
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <link ref="stylesheet" href="{% static 'style.css' %}" />
        <title>{% block title %}我的站点{% endblock %}</title>
    </head>
    
    <body>
        <div id="sidebar">
            {% block sidebar %}
            <ul>
                <li><a href="/">首页<a></li>
                <li><a href="/blog/">博客<a></li>
            </ul>
            {% endblock %}
        </div>
        
        <div id="content">
            {% block content %} {% endblock %}
        </div>
    </body>
    </html>
```
这个模板，我们取名为`base.html`，定义好一个简单的`html`骨架，然后定义好两个`block`接口，让子模板来根据具体需要来实现。子模板然后通过`extends`标签来实现，示例代码如下：
```html
    {% extends "base.html" %}
    
    {% block title %}博客列表{% endblock %}
    
    {% block content %}
        {% for entry in blog_entries %}
            <h2> {{ entry.title }}</h2>
            <p>{{ entry.body }}</p>
        {% endfor %}
    {% endblock%}
```
需要注意的是：**extends**标签必须放在模板的第一行。
子模板中的代码必须放在**block**中，否则将不会被渲染
如果某个`block`中需要使用父模板中的内容，那么可以使用`{{ block.super }}`来继承。比如上例，`{% block title %}`，想要使用父模板的`title`，那么可以在子模板的`title block`中使用`{{ block.super }}`
