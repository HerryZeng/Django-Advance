# HttpResponse对象

`Django`服务器接收到客户端发送过来的请求后，会将提交上来的这些数据封装成一个 `HttpRequest`对象传给视图函数。那么视图函数在处理完相关的逻辑后，也需要返回一个响应
给浏览器。而这个响应，我们必须返回 `HttpResponseBase`或者他的子类的对象。而 `HttpResponse`则是 `HttpResponseBase`用得最多的子类。那么接下来就来介绍一下 `HttpResponse`及其子类。

## 常用属性

1. `content`：返回的内容。
2. `status_code`：返回的HTTP响应状态码。
3. `content_type`：返回的数据的MIME类型，默认为 `text/html` 。浏览器会根据这个属性，来显示数据。如果是 `text/html` ，那么就会解析这个字符串，如果`text/plain` ，那么就会显示一个纯文本。常用的 `Content-Type` 如下：
+ text/html（默认的，html文件）
+ text/plain（纯文本）
+ text/css（css文件）
+ text/javascript（js文件）
+ multipart/form-data（文件提交）
+ application/json（json传输）
+ application/xml（xml文件）
4. 设置请求头： response['X-Access-Token'] = 'xxxx' 。