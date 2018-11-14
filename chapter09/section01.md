## CSRF攻击

### CSRF攻击概述

`CSRF（Cross Site Request Forgery, 跨站域请求伪造）`是一种网络的攻击方式，它在 2007 年曾被列为互联网 20 大安全隐患之一。其他安全隐患，比如 `SQL`脚本注入，跨站域脚本攻击等在近年来已经逐渐为众人熟知，很多网站也都针对他们进行了防御。然而，对于大多数人来说，`CSRF`却依然是一个陌生的概念。即便是大名鼎鼎的 Gmail, 在 2007 年底也存在着 CSRF 漏洞，从而被黑客攻击而使 Gmail 的用户造成巨大的损失。

### CSRF攻击原理

网站是通过`cookie`来实现登录功能的。而`cookie`只要存在浏览器中，那么浏览器在访问这个`cookie`的服务器的时候，就会自动的携带`cookie`信息到服务器上去。那么这时候就存在一个漏洞了，如果你访问了一个别有用心或病毒网站，这个网站可以在网页源代码中插入`js`代码，使用`js`代码给其他服务器发送请求（比如ICBC的转账请求）。那么因为在发送请求的时候，浏览器会自动的把cookie发送给对应的服务器，这时候相应的服务器（比如ICBC网站），就不知道这个请求是伪造的，就被欺骗过去了。从而达到在用户不知情的情况下，给某个服务器发送了一个请求（比如转账）。

### 防御CSRF攻击

`CSRF`攻击的要点就是在向服务器发送请求的时候，相应的`cookie`会自动的发送给对应的服务器。造成服务器不知道这个请求是用户发起的还是伪造的。这时候，我们可以在用户每次访问有表单的页面的时候，在网页源代码中加一个随机的字符串叫做`csrf_token`，在`cookie`中也加入一个相同值的`csrf_token`字符串。以后给服务器发送请求的时候，必须在`body`中以及`cookie`中都携带`csrf_token`，服务器只有检测到`cookie`中的`csrf_token`和`body`中的`csrf_token`都相同，才认为这个请求是正常的，否则就是伪造的。那么黑客就没办法伪造请求了。在`Django`中，如果想要防御CSRF攻击，应该做两步工作。第一个是在`settings.MIDDLEWARE`中添加`CsrfMiddleware`中间件。第二个是在模版代码中添加一个`input`标签，加载`csrf_token`。示例代码如下：
+ 服务器代码:
```python
    MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.middleware.gzip.GZipMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware'
    ]
```
+ 模版代码
```html
    <input type="hidden" name="csrfmiddlewaretoken" value="{{ csrf_token }}"/>
```
或者是直接使用csrf_token标签，来自动生成一个带有csrf token的input标签：
```html
    {% csrf_token %}
```

### 使用ajax处理csrf防御

如果用`ajax`来处理`csrf`防御，那么需要手动的在`form`中添加`csrfmiddlewaretoken`，或者是在请求头中添加`X-CSRFToken`。我们可以从返回的`cookie`中提取`csrf token`，再设置进去。示例代码如下：
```python
    function getCookie(name) {
    var cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        var cookies = document.cookie.split(';');
        for (var i = 0; i < cookies.length; i++) {
            var cookie = jQuery.trim(cookies[i]);
            // Does this cookie string begin with the name we want?
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
    }
```


### CSRF和AJAX

我们知道，在前端的世界，有一种叫做AJAX的东西，也就是`Asynchronous JavaScript And XML`（异步JavaScript和XML），经常被用来在不刷新页面的情况下，提交和请求数据。如果我们的Django服务器接收的是一个通过AJAX发送过来的`POST`请求的话，那将很麻烦。
为什么？ 因为AJAX中，没有办法像`form`表单中那样携带`{% csrf_token %}`令牌。那怎么办呢？
好办，在你的前端模板的JavaScript代码处，添加下面的代码：
```javascript
// using jQuery
function getCookie(name){
    var cookieValue = null;
    if (document.cookie && document.cookie != ''){
        var cookies = document.cookie.split(';');
        for (var i=0;i<cookies.length;i++){
            var cookie = jQuery.trim(cookies[i]);
            if (cookie.substring(0,name.length + 1 === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue
}

var csrftoken = getCookie('csrftoken')

function csrfSafeMethod(method){
    return (/^(GET|HEAD|OPTIONS|TRACE$/).test(method));
}

$.ajaxSetup({
    beforeSend: function(xhr,settings){
        if(!csrfSafeMethod(settings.type) && ! this.crossDomain) {
            xhr.setRequestHeader("X-CSRFToken",csrftoken);
        }
    }
});
```
上面代码的作用就是让你的AJAX的`POST`方法带上`CSRF`需要的令牌，它依赖`jQuery`。这是Django的官方提供的解决方案。



### iframe相关知识

1. `iframe`可以加载嵌入别的域名下的网页。也就是说可以发送跨域请求。比如我可以在我自己的网页中加载百度的网站，示例代码如下：
```html
    <iframe src="http://www.baidu.com/">
    </ifrmae>
```
2. 因为`iframe`加载的是别的域名下的网页。根据同源策略，`js`只能操作属于本域名下的代码，因此`js`不能操作通过`iframe`加载来的`DOM`元素。
3. 如果`ifrmae`的`src`属性为空，那么就没有同源策略的限制，这时候我们就可以操作`iframe`下面的代码了。并且，如果`src`为空，那么我们可以在`iframe`中，给任何域名都可以发送请求。
4. 直接在`iframe`中写`html`代码，浏览器是不会加载的。