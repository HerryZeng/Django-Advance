## URL组成部分详解

`URL`是`Uniform Resource Locator`的简写，统一资源定位符。

一个`URL`由以下几部分组成：
```
    scheme://host:port/path/?query-string=xxx#anchor
```
    + `scheme`:代表的是访问的协议，一般为`http`或`https`以及`ftp`等。
    + `host`: 主机名，域名，比如`www.baidu.com`
    + `port`: 端口号。当你访问一个网站的时候，浏览器默认使用`80`端口。
    + `path`: 查找路径。比如：`www.jianshu.com/thrending/now`，后面的`trending/now`就是`path`。
    + `query-string`: 查询字符串，比如`www.baidu.com/s?wd=python`，后面的`wd=python`就是查询字符串。
    + `anchor`: 锚点，后台一般不用管，前端用来做页面定位的。
    
注意：`URL`中的所有字符都是`ASCII`字符集，如果出现非`ASCII`字符，比如中文，浏览器会进行编码再进行传输。