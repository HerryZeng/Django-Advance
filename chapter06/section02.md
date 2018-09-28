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
