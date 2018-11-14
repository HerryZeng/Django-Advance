## AJAX

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