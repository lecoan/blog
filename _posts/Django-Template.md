# Django - Template

## 引擎

### 配置

模板引擎通过[`TEMPLATES`](http://usyiyi.cn/documents/Django_111/ref/settings.html#std:setting-TEMPLATES) 设置来配置。 它是一个设置选项列表，与引擎一一对应。 默认的值为空。 由[`startproject`](http://usyiyi.cn/documents/Django_111/ref/django-admin.html#django-admin-startproject) 命令生成的`settings.py` 定义了一些有用的值：

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            # ... some options here ...
        },
    },
]
```

[`BACKEND`](http://usyiyi.cn/documents/Django_111/ref/settings.html#std:setting-TEMPLATES-BACKEND) 是一个指向实现了Django模板后端API的模板引擎类的带点的Python路径。 内置的后端有 [`django.template.backends.django.DjangoTemplates`](http://usyiyi.cn/documents/Django_111/topics/templates.html#django.template.backends.django.DjangoTemplates) 和 [`django.template.backends.jinja2.Jinja2`](http://usyiyi.cn/documents/Django_111/topics/templates.html#django.template.backends.jinja2.Jinja2)。

由于绝大多数引擎都是从文件加载模板的，所以每种模板引擎都包含两项通用设置：

- [`DIRS`](http://usyiyi.cn/documents/Django_111/ref/settings.html#std:setting-TEMPLATES-DIRS) 定义了一个目录列表，模板引擎按列表顺序搜索这些目录以查找模板源文件。
- [`APP_DIRS`](http://usyiyi.cn/documents/Django_111/ref/settings.html#std:setting-TEMPLATES-APP_DIRS) 告诉模板引擎是否应该进入每个已安装的应用中查找模板。 每种模板引擎后端都定义了一个惯用的名称作为应用内部存放模板的子目录名称。（译者注：例如django为它自己的模板引擎指定的是 ‘templates’ ，为jinja2指定的名字是‘jinja2’）

特别的是，django允许你有多个模板引擎后台实例，且每个实例有不同的配置选项。

### 用法

## 语法

```jinja2
{% extends "base_generic.html" %}

{% block title %}{{ section.title }}{% endblock %}

{% block content %}
<h1>{{ section.title }}</h1>

{% for story in story_list %}
<h2>
  <a href="{{ story.get_absolute_url }}">
    {{ story.headline|upper }}
  </a>
</h2>
<p>{{ story.tease|truncatewords:"100" }}</p>
{% endfor %}
{% endblock %}
```

### 变量

变量看起来就像是这样： `{{ variable }}`。 当模版引擎遇到一个变量，它将计算这个变量，然后用结果替换掉它本身。 变量的命名包括任何字母数字以及下划线 (`"_"`)的组合。 点(`"."`) 也在会变量部分中出现，不过它有特殊的含义

### 过滤器

过滤器看起来是这样的：`{{ name|lower }}`。 

过滤器可以“链接”。一个过滤器的输出应用于下一个过滤器。 `{{ text|escape|linebreaks }}` 就是一个常用的过滤器链，它编码文本内容，然后把行打破转成`<p>` 标签。

一些过滤器带有参数。 过滤器的参数看起来像是这样： `{{ bio|truncatewords:30 }}`。 这将显示 `bio` 变量的前30个词。

过滤器参数包含空格的话，必须被引号包起来；例如，使用逗号和空格去连接一个列表中的元素，你需要使用 `{{ list|join:", " }}`。

Django提供了大约六十个内置的模版过滤器。 你可以在 [built-in filter reference](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#ref-templates-builtins-filters)中阅读全部关于它们的信息。

- [`default`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatefilter-default)

  如果一个变量是false或者为空，使用给定的默认值。 否则，使用变量的值。 像这样：`{{ value|default:"nothing" }}`如果 `value`没有被提供，或者为空， 上面的例子将显示“`nothing`”。

- [`length`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatefilter-length)

  返回值的长度。 它对字符串和列表都起作用。 像这样：`{{ value|length }}`如果 `4` 是 `['a', 'b', 'c', 'd']`，那么输出是 `value`。

- [`filesizeformat`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatefilter-filesizeformat)

  格式化为“人类可读”文件大小（即`'13 KB'`，`t4> MB'`，`'102 bytes'`等）。 像这样：`{{ value|filesizeformat }}`如果 `value` 是 123456789，输出将会是 `117.7 MB`。

### 标记

for

```django
<ul>
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% endfor %}
</ul>
```

if, elif, else

```django
{% if athlete_list %}
    Number of athletes: {{ athlete_list|length }}
{% elif athlete_in_locker_room_list %}
    Athletes should be out of the locker room soon!
{% else %}
    No athletes.
{% endif %}
```

block extends

参见模板继承，一种减少模板样板文件的有效方法。

### 注释

要注释模版中一行的部分内容，使用注释语法 `{# #}`.

例如，这个模版将被渲染为 `'hello'`：

```django
{# greeting #}hello
```

注释可以包含任何模版代码，有效的或者无效的都可以。 像这样：

```django
{# {% if foo %}bar{% else %} #}
```

这个语法只能被用于单行注释

### 转义

当从模版中生成HTML时，总会有这样一个风险：值可能会包含影响HTML最终呈现的字符

默认情况下，Django 中的每个模板会自动转义每个变量的输出。 明确地说，下面五个字符被转义：

- `<` 会转换为`&lt;`
- `>` 会转换为`&gt;`
- `'`（单引号）转换为`&#39;`
- `"` (双引号)会转换为 `&quot;`
- `&` 会转换为 `&amp;`

#### 对于单个变量[¶](http://usyiyi.cn/documents/Django_111/ref/templates/language.html#for-individual-variables)

使用[`safe`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatefilter-safe)过滤器来关闭独立变量上的自动转义：

```django
This will be escaped: {{ data }}
This will not be escaped: {{ data|safe }}
```

要控制模板上的自动转义，将模板（或者模板中的特定区域）包裹在[`autoescape`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatetag-autoescape)标签 中，像这样：

```django
{% autoescape off %}
    Hello {{ name }}
{% endautoescape %}
```

像我们之前提到的那样，过滤器参数可以是字符串：

```django
{{ data|default:"This is a string literal." }}
```

所有字面值字符串在插入模板时都 **不会**带有任何自动转义 -- 它们的行为类似于通过 [`safe`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatefilter-safe)过滤器传递

也即是说你应该这样编写

```django
{{ data|default:"3 &lt; 2" }}
```

…而不是：

```django
{{ data|default:"3 < 2" }}  {# Bad! Don't do this. #}
```

### 方法调用

你可以轻易访问已经显式定义在你自己模型上的方法：

models.py

```python
class Task(models.Model):
    def foo(self):
        return "bar"
```

template.html

```django
{{ task.foo }}
```

由于Django 有意限制了模板语言中的逻辑处理，不能够在模板中传递参数来调用方法。 数据应该在视图中处理，然后传递给模板用于展示。

### 自定义标签和过滤库

 要在模板中访问它们，确保应用已经在[`INSTALLED_APPS`](http://usyiyi.cn/documents/Django_111/ref/settings.html#std:setting-INSTALLED_APPS) 中（在这个例子中我们添加了`'django.contrib.humanize'`），之后在模板中使用[`load`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatetag-load)标签：

```django
{% load humanize %}

{{ 45000|intcomma }}
```