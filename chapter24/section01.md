## 使用Django发送邮件

通常而言，我们在用户注册成功，实际登陆之前，会发送一封电子邮件到对方的注册邮箱中，表示欢迎。进一步的还可能要求用户点击邮件中的链接，进行注册确认。

下面就让我们先看看如何在Django中发送邮件吧。

---

### 一、在Django中发送邮件

其实在Python中已经内置了一个smtp邮件发送模块，Django在此基础上进行了简单地封装。

首先，我们需要在项目的`settings`文件中配置邮件发送参数，分别如下：
```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.sina.com'
EMAIL_PORT = 25
EMAIL_HOST_USER = 'xxx@sina.com'
EMAIL_HOST_PASSWORD = 'xxxxxxxxxxx'
```
    + 第一行指定发送邮件的后端模块，大多数情况下照抄！
    + 第二行，不用说，发送方的smtp服务器地址，建议使用新浪家的；
    + 第三行，smtp服务端口，默认为25；
    + 第四行，你在发送服务器的用户名；
    + 第五行，对应用户的密码。
    
特别说明：
    + 某些邮件公司可能不开放smtp服务
    + 某些公司要求使用ssl安全机制
    + 某些smtp服务对主机名格式有要求
    
这些都是前人踩过的坑。

配置好了参数，就可以先测试一下邮件功能了。

在项目根目录下新建一个`send_mail.py`文件，然后写入下面的内容：

```
import os
import sys
from django.core.mail import send_mail
from django.conf import settings


sys.path.append(settings.BASE_DIR)

os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'

if __name__ == '__main__':   

    send_mail(
        '来自www.liujiangblog.com的测试邮件',
        '欢迎访问www.liujiangblog.com，这里是刘江的博客和教程站点，本站专注于Python和Django技术的分享！',
        'xxx@sina.com',
        ['xxx@qq.com'],
    )
```