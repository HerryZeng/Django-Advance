## 验证和授权概述

`Django`有一个内置的授权系统。他用来处理用户、分组、权限以及基于`cookie`的会话系统。`Django`的授权系统包括验证和授权两个部分。验证是验证这个用户是否是他声称的人（比如用户名和密码验证，角色验证），授权是给与他相应的权限。`Django`内置的权限系统包括以下方面：
1. 用户。
2. 权限。
3. 分组。
4. 一个可以配置的密码哈希系统。
5. 一个可插拔的后台管理系统。

## 使用授权系统

默认中创建完一个`django`项目后，其实就已经集成了授权系统。那哪些部分是跟授权系统相关的配置呢。以下做一个简单列表：

`INSTALLED_APPS`:
1. `django.contrib.auth`：包含了一个核心授权框架，以及大部分的模型定义。
2. `django.contrib.contenttypes`：Content Type系统，可以用来关联模型和权限。

`中间件`
1. `SessionMiddleware`：用来管理`session`。
2. `AuthenticationMiddleware`：用来处理和当前`session`相关联的用户。