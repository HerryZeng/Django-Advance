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