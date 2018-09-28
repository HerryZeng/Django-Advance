# 用表单验证数据

## 常用的Field

使用 `Field`可以是对数据验证的第一步。你期望这个提交上来的数据是什么类型，那么就使用什么类型的 Field 。

### CharField

用来接收文本。
参数：
+ max_length：这个字段值的最大长度。
+ min_length：这个字段值的最小长度。
+ required：这个字段是否是必须的。默认是必须的。
+ error_messages：在某个条件验证失败的时候，给出错误信息。

### EmailField

用来接收邮件，会自动验证邮件是否合法。
错误信息的key： required 、 invalid。

### FloatField

用来接收浮点类型，并且如果验证通过后，会将这个字段的值转换为浮点类型。参数：
+ max_value：最大的值。
+ min_value：最小的值。
错误信息的key： `required`、 `invalid`、 `max_value`、 `min_value`。

### IntegerField

用来接收整形，并且验证通过后，会将这个字段的值转换为整形。参数：
+ max_value：最大的值。
+ min_value：最小的值。
错误信息的key： `required`、 `invalid`、 `max_value`、 `min_value`。

### URLField

用来接收 url 格式的字符串。
错误信息的key： `required`、 `invalid `。


## 常用验证器

在验证某个字段的时候，可以传递一个 `validators`参数用来指定验证器，进一步对数据进行过滤。验证器有很多，但是很多验证器我们其实已经通过这个 Field 或者一些参数就可以指定了。比如 `EmailValidator`，我们可以通过 `EmailField`来指定，比如 `MaxValueValidator`，我们可以通过 `max_value`参数来指定。以下是一些常用的验证器：
1. `MaxValueValidator`：验证最大值。
2. `MinValueValidator`：验证最小值。
3. `MinLengthValidator`：验证最小长度。
4. `MaxLengthValidator`：验证最大长度。
5. `EmailValidator`：验证是否是邮箱格式。
6. `URLValidator`：验证是否是 URL 格式。
7. `RegexValidator`：如果还需要更加复杂的验证，那么我们可以通过正则表达式的验证器： RegexValidator 。比如现在要验证手机号码是否合格，那么我们可以通过以下代码实现：
```python
    class MyForm(forms.Form):
        telephone = forms.CharField(validators=[validators.RegexValidator("1[345678]\d{9}",message='请输入正确格式的手机号码！')])
```

## 自定义验证

有时候对一个字段验证，不是一个长度，一个正则表达式能够写清楚的，还需要一些其他复杂的逻辑，那么我们可以对某个字段，进行自定义的验证。比如在注册的表单验证中，我们想要验证手机号码是否已经被注册过了，那么这时候就需要在数据库中进行判断才知道。对某个字段进行自定义的验证方式是，定义一个方法，这个方法的名字定义规则是： `clean_fieldname`。如果验证失败，那么就抛出一个验证错误。比如要验证用户表中手机号码之前是否在数据库中存在，那么可以通过以下代码实现：
```python
    class MyForm(forms.Form):
    telephone = forms.CharField(validators=[validators.RegexValidator("1[345678]\d{9}",message='请输入正确格式的手机号码！')])
    
    def clean_telephone(self):
        telephone = self.cleaned_data.get('telephone')
        exists = User.objects.filter(telephone=telephone).exists()
        if exists:
            raise forms.ValidationError("手机号码已经存在！")
        return telephone
```
