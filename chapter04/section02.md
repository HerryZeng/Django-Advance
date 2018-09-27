# 操作数据库

## Django配置连接数据库

在操作数据库之前，首先要连接数据库。这我们以配置`MySQL`为例来讲解。`Django`连接数据库，不需要单独的创建一个连接对象。只需要在`settings.py`文件中做好数据库相关的配置就可以了。示例如下：
```python
    DATABASES ={
        'default':{
            # 数据库引擎（是mysql还是oracle等）
            'ENGINE': 'django.db.backends.mysql',
            # 数据库的名字
            'NAME': 'abc',
            # 连接mysql数据库的用户名
            'USER': 'root',
            # 连接mysql的数据的密码
            'PASSWORD': '12345678',
            # mysql数据库的主机地址
            'HOST': '127.0.0.1',
            # mysql数据库的端口号
            'PORT':'3306',
        }
    }
```
