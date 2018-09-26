# DTL模板语法

## 变量

模板中可以包含变量，`Django`在渲染模板的时候，可以传递变量对应的值过去进行替换。变量的命名规范和`Python`非常类似，只能是阿拉伯数字和英文字符以及下划线的组合，不能出现标点符号等特殊符号。变量需要通过视图函数渲染，视图函数在使用`render`或`render_to_string`的时候可以传递一个`context`的参数，这个参数是一个字典类型。以后在模板中的变量从这个字典中读取值的。示例代码如下：
```Python
# profile.html模板代码
<p>{{ username }} </p>

# views.py代码
def profile(request):
    return render(request,'profile.html',context={'username':'hanmeimei'})
```