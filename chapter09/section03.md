## clickjacking攻击

`clickjacking`攻击又称作点击劫持攻击。是一种在网页中将恶意代码等隐藏在看似无害的内容（如按钮）之下，并诱使用户点击的手段。

### clickjacking攻击场景

+ 场景一

如用户收到一封包含一段视频的电子邮件，但其中的“播放”按钮并不会真正播放视频，而是链入一购物网站。这样当用户试图“播放视频”时，实际是被诱骗而进入了一个购物网站。

+ 场景二

用户进入到一个网页中，里面包含了一个非常有诱惑力的`按钮A`，但是这个按钮上面浮了一个透明的`iframe`标签，这个`iframe`标签加载了另外一个网页，并且他将这个网页的某个按钮和原网页中的`按钮A`重合，所以你在点击`按钮A`的时候，实际上点的是通过`iframe`加载的另外一个网页的按钮。比如我现在有一个百度贴吧，想要让更多的用户来关注，那么我们可以准备以下一个页面：
```html
    <!DOCTYPE html>
    <html>
    <meta http-equiv="Content-Type" content="text/html; charset=gb2312" />
    <head>
    <title>点击劫持</title>
    <style>
        iframe{
            opacity:0.01;
            position:absolute;
            z-index:2;
            width: 100%;
            height: 100%;
        }
        button{
            position:absolute;
            top: 345px;
            left: 630px;
        }
```
```html
<html>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />
<head>
<title>点击劫持</title>
<style>
    iframe{
        opacity:0.01;
        position:absolute;
        z-index:2;
        width: 100%;
        height: 100%;
    }
    button{
        position:absolute;
        top: 345px;
        left: 630px;
        z-index: 1;
        width: 72px;
        height: 26px;
    }
</style>
</head>
<body>
    这个合影里面怎么会有你？
    <button>查看详情</button>
    <iframe src="http://tieba.baidu.com/f?kw=%C3%C0%C5%AE"></iframe>
</body>
</html>
```
页面看起来比较简陋，但是实际上可能会比这些更精致一些。当这个页面通过某种手段被传播出去后，用户如果点击了“查看详情”，实际上点击到的是关注的按钮，这样就可以增加了一个粉丝。

### clickjacking防御

像以上场景1，是没有办法避免的，受伤害的是用户。而像场景2，受伤害的是百度贴吧网站和用户。这种场景是可以避免的，只要设置百度贴吧不允许使用`iframe`被加载到其他网页中，就可以避免这种行为了。我们可以通过在响应头中设置`X-Frame-Options`来设置这种操作。`X-Frame-Options`可以设置以下三个值： 
1. `DENY`：不让任何网页使用`iframe`加载我这个页面。 
2. `SAMEORIGIN`：只允许在相同域名（也就是我自己的网站）下使用`iframe`加载我这个页面。 
3. `ALLOW-FROM origin`：允许任何网页通过`iframe`加载我这个网页。
在`Django`中，使用中间件`django.middleware.clickjacking.XFrameOptionsMiddleware`可以帮我们堵上这个漏洞，这个中间件设置了`X-Frame-Option`为`SAMEORIGIN`，也就是只有在自己的网站下才可以使用`iframe`加载这个网页，这样就可以避免其他别有心机的网页去通过iframe去加载了。