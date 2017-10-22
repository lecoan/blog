#  Django - form

## HTML - form

在HTML中,表单的作用是收集标签中的内容,`<form>...</form>` 中间可以由访问者添加类似于文本,选择,或者一些控制模块等等.然后这些内容将会被送到服务端

与`<input>` 元素一样，一个表单必须指定两样东西：

- *目的地*：响应用户输入数据的URL
- *方式*：发送数据所使用的HTTP 方法

处理表单时候只会用到`POST` 和 `GET` 方法。`POST` 和`GET` 用于不同的目的。

用于改变系统状态的请求 —— 例如，给数据库带来变化的请求 —— 应该使用`POST`。 `GET` 只应该用于不会影响系统状态的请求。

## Django - form

我们已经简短讲述HTML 表单，但是HTML的`<form>` 只是其机制的一部分。

在一个Web 应用中，"表单"可能指HTML `<form>`、或者生成它的Django 的[`Form`](http://usyiyi.cn/documents/Django_111/ref/forms/api.html#django.forms.Form)、或者提交时发送的结构化数据、或者这些部分的总和。

### Django From Class

表单系统的核心部分是Django 的[`Form`](http://usyiyi.cn/documents/Django_111/ref/forms/api.html#django.forms.Form) 类。[`Form`](http://usyiyi.cn/documents/Django_111/ref/forms/api.html#django.forms.Form) 类描述一个表单并决定它如何工作和展现。

就像模型类的属性映射到数据库的字段一样，表单类的字段会映射到HTML 的`<input>`表单的元素。

### 实例化，处理和呈现表单

在Django 中渲染一个对象时，我们通常：

1. 在视图中获得它（例如，从数据库中获取）
2. 将它传递给模板上下文
3. 使用模板变量将它扩展为HTML 标记

## 构建一个Form

### From Class

```python
from django import forms

class NameForm(forms.Form):
    your_name = forms.CharField(label='Your name', max_length=100)
```

[`Form`](http://usyiyi.cn/documents/Django_111/ref/forms/api.html#django.forms.Form) 的实例具有一个[`is_valid()`](http://usyiyi.cn/documents/Django_111/ref/forms/api.html#django.forms.Form.is_valid) 方法，它为所有的字段运行验证的程序。 当调用这个方法时，如果所有的字段都包含合法的数据，它将：

- 返回`True`
- 将表单的数据放到[`cleaned_data`](http://usyiyi.cn/documents/Django_111/ref/forms/api.html#django.forms.Form.cleaned_data) 属性中。

完整的表单，第一次渲染时，看上去将像：

```html
<label for="your_name">Your name: </label>
<input id="your_name" type="text" name="your_name" maxlength="100" required />
```

如果你的表单包含[`URLField`](http://usyiyi.cn/documents/Django_111/ref/forms/fields.html#django.forms.URLField)、[`EmailField`](http://usyiyi.cn/documents/Django_111/ref/forms/fields.html#django.forms.EmailField) 或其它整数字段类型，Django 将使用`number`、`url`和 `email` 这样的HTML5 输入类型。 默认情况下，浏览器可能会对这些字段进行它们自身的验证，这些验证可能比Django 的验证更严格。 如果你想禁用这个行为，请设置`form` 标签的`novalidate`属性，或者指定一个不同的字段，如[`TextInput`](http://usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput)。

### View

```python
from django.shortcuts import render
from django.http import HttpResponseRedirect

from .forms import NameForm

def get_name(request):
    # if this is a POST request we need to process the form data
    if request.method == 'POST':
        # create a form instance and populate it with data from the request:
        form = NameForm(request.POST)
        # check whether it's valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required
            # ...
            # redirect to a new URL:
            return HttpResponseRedirect('/thanks/')

    # if a GET (or any other method) we'll create a blank form
    else:
        form = NameForm()

    return render(request, 'name.html', {'form': form})
```

### HTML

```django
<form action="/your-name/" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit" value="Submit" />
</form>
```

当提交一个启用CSRF 防护的`POST` 表单时，你必须使用上面例子中的[`csrf_token`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatetag-csrf_token) 模板标签。

#### 使用标签

对于`<input>`/`<label>` 对，还有几个输出选项：

- `{{ form.as_table }}` 以表格的形式将它们渲染在`<tr>` 标签中
- `{{ form.as_p }}` 将它们渲染在`<p>` 标签中
- `{{ form.as_ul }}` 将它们渲染在`<li>` 标签中

注意，你必须自己提供`<ul>` 或`<table>` 元素。

#### 自定义

我们不必让Django打开表单的字段；如果我们喜欢，我们可以手动执行（例如，允许我们重新排序字段）。 每个字段都是表单的一个属性，可以使用`{{ form.name_of_field }}` 访问，并将在Django 模板中正确地渲染。

```django
{{ form.non_field_errors }}
<div class="fieldWrapper">
    {{ form.subject.errors }}
    <label for="{{ form.subject.id_for_label }}">Email subject:</label>
    {{ form.subject }}
</div>
<div class="fieldWrapper">
    {{ form.message.errors }}
    <label for="{{ form.message.id_for_label }}">Your message:</label>
    {{ form.message }}
</div>
<div class="fieldWrapper">
    {{ form.sender.errors }}
    <label for="{{ form.sender.id_for_label }}">Your email address:</label>
    {{ form.sender }}
</div>
<div class="fieldWrapper">
    {{ form.cc_myself.errors }}
    <label for="{{ form.cc_myself.id_for_label }}">CC yourself?</label>
    {{ form.cc_myself }}
</div>
```

#### 处理错误

使用`{{ form.name_of_field.errors }}` 显示表单错误的一个清单，并渲染成一个ul。 看上去可能像：

```html
<ul class="errorlist">
    <li>Sender is required.</li>
</ul>
```

这个ul 有一个`errorlist` CSS 类型，你可以用它来定义外观。

如果你为你的表单使用相同的HTML，你可以使用`{% for filed in form %}` 循环迭代每个字段来减少重复的代码

`{{ field }}` 中有用的属性包括：

- `{{ field.label }}`

  字段的label，例如`Email address`。

- `{{ field.label_tag }}`

  包含在HTML `<label>` 标签中的字段Label。 它包含表单的[`label_suffix`](http://usyiyi.cn/documents/Django_111/ref/forms/api.html#django.forms.Form.label_suffix)。 例如，默认的`label_suffix` 是一个冒号

- `{{ field.id_for_label }}`

  用于这个字段的ID（在上面的例子中是`id_email`）。 如果你正在手工构造label，你可能想使用它代替`label_tag`。 如果你有一些内嵌的JavaScript 并且想避免硬编码字段的ID，这也是有用的。

- `{{ field.value }}`

  字段的值。 例如`someone@example.com`。

- `{{ field.html_name }}`

  输入元素的name 属性中将使用的名称。 它将考虑到表单的前缀。

- `{{ field.help_text }}`

  与该字段关联的帮助文档。

- `{{ field.errors }}`

  输出一个`<ul class="errorlist">`，包含这个字段的验证错误信息。 你可以使用`{% for error in field.errors %}`自定义错误的显示。 这种情况下，循环中的每个对象只是一个包含错误信息的简单字符串。

- `{{ field.is_hidden }}`

  如果字段是隐藏字段，则为`False`，否则为`True`。

Django 提供两个表单方法，它们允许你独立地在隐藏的和可见的字段上迭代：`visible_fields()` 和`hidden_fields()`。 

如果你的网站在多个地方对表单使用相同的渲染逻辑，你可以保存表单的循环到一个单独的模板中来减少重复，然后在其它模板中使用[`include`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatetag-include) 标签来重用它

如果传递到模板上下文中的表单对象具有一个不同的名称，你可以使用[`include`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatetag-include) 标签的`with` 参数来对它起个别名：

```django
{% include "form_snippet.html" with form=comment_form %}
```

如果你发现自己经常这样做，你可能需要考虑一下创建一个自定义的 [inclusion tag](http://usyiyi.cn/documents/Django_111/howto/custom-template-tags.html#howto-custom-template-tags-inclusion-tags)。

## 表单集 ?

## Create Forms From Model

果你正在构建一个数据库驱动的应用，那么你应该会有与Django 的模型紧密映射的表单。基于这个原因，Django 提供一个辅助类来让你可以从Django 的模型创建`Form`。

```python
from django.forms import ModelForm
from myapp.models import Article

# Create the form class.
class ArticleForm(ModelForm):
     class Meta:
         model = Article
         fields = ['pub_date', 'headline', 'content', 'reporter']

# Creating a form to add an article.
form = ArticleForm()

# Creating a form to change an existing article.
article = Article.objects.get(pk=1)
form = ArticleForm(instance=article)
```

生成的`fields`类中将具有和指定的模型字段对应的表单字段，顺序为`Form` 属性中指定的顺序

`ManyToManyField` 和 `ForeignKey` 字段类型属于特殊情况：

- `QuerySet` 表示成`ChoiceField`，它是一个`django.forms.ModelChoiceField`，其选项是模型的`ForeignKey`。
- `QuerySet` 表示成`MultipleChoiceField`，它是一个`django.forms.ModelMultipleChoiceField`，其选项是模型的`ManyToManyField`。

此外，生成的每个表单字段都有以下属性集：

- 如果模型字段设置`False`，那么表单字段的`required` 设置为`blank=True`。 否则，`required=True`。
- 表单字段的`verbose_name` 设置为模型字段的`label`，并将第一个字母大写。
- 表单字段的`help_text` 设置为模型字段的`help_text`。
- 如果模型字段设置了`choices`，那么表单字段的`Select` 将设置成`widget`，其选项来自模型字段的`choices`。 选项通常会包含空选项，并且会默认选择。 如果字段是必选的，它会强制用户选择一个选项。 如果模型字段的`default` 且具有一个显示的`default` 值，将不会包含空选项（初始将选择`blank=False` 值）。

## 引入样式 ?

```python
from django import forms

class CalendarWidget(forms.TextInput):
    class Media:
        css = {
            'all': ('pretty.css',)
        }
        js = ('animations.js', 'actions.js')
```

面的代码定义了 `TextInput`，它继承于`CalendarWidget`。 每次`CalendarWidget`在表单上使用时，表单都会包含CSS文件`actions.js`，以及JavaScript文件`animations.js` 和 `pretty.css`。

