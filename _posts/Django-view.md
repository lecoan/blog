---
title: Django中的View
date: 2017-10-10 11:47:14
tags: 
- Django
- Python
---

# Django - view - urls

下面是Django 系统决定执行哪个Python 代码遵循的算法：

1. Django 决定要使用的根URLconf 模块。 通常，这是[`ROOT_URLCONF`](http://usyiyi.cn/documents/Django_111/ref/settings.html#std:setting-ROOT_URLCONF)设置的值，但是如果传入的`HttpRequest`对象具有[`urlconf`](http://usyiyi.cn/documents/Django_111/ref/request-response.html#django.http.HttpRequest.urlconf)属性（由中间件设置），则其值将被用于代替[`ROOT_URLCONF`](http://usyiyi.cn/documents/Django_111/ref/settings.html#std:setting-ROOT_URLCONF)设置。

2. Django 加载该Python 模块并寻找可用的`urlpatterns`。 它是[`django.conf.urls.url()`](http://usyiyi.cn/documents/Django_111/ref/urls.html#django.conf.urls.url) 实例的一个Python 列表。

3. Django 依次匹配每个URL 模式，在与请求的URL 匹配的第一个模式停下来。

4. 一旦正则表达式匹配，Django将导入并调用给定的视图，该视图是一个简单的Python函数（或基于类的[class-based view](http://usyiyi.cn/documents/Django_111/topics/class-based-views/index.html)）。

<!--more-->
    

   视图将获得如下参数:

   - 一个[`HttpRequest`](http://usyiyi.cn/documents/Django_111/ref/request-response.html#django.http.HttpRequest) 实例。
   - 如果匹配的正则表达式返回了没有命名的组，那么正则表达式匹配的内容将作为位置参数提供给视图。
   - 关键字参数由正则表达式匹配的命名组组成，但是可以被[`django.conf.urls.url()`](http://usyiyi.cn/documents/Django_111/ref/urls.html#django.conf.urls.url)的可选参数`kwargs`覆盖。

5. 如果没有匹配到正则表达式，或者如果过程中抛出一个异常，Django 将调用一个适当的错误处理视图

注意

- 若要从URL 中捕获一个值，只需要在它周围放置一对圆括号。
- `urlpatterns` 应该是一个[`url()`](http://usyiyi.cn/documents/Django_111/ref/urls.html#django.conf.urls.url) 实例类型的Python 列表。
- 不需要添加一个前导的反斜杠，因为每个URL 都有。 例如，应该是`^articles` 而不是 `^/articles`。
- 每个正则表达式前面的`'r'` 是可选的但是建议加上。 它告诉Python 这个字符串是“原始的” —— 字符串中任何字符都不应该转义。
- 每个模式要求URL 以一个斜线结尾

## 命名组

在Python 正则表达式中，命名正则表达式组的语法是`(?P<name>pattern)`，其中`pattern` 是组的名称，`name` 是要匹配的模式。

```python
url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<day>[0-9]{2})/$', views.article_detail)
```

- `/articles/2003/03/03/` 请求将调用函数`views.article_detail(request, year='2003', month='03', day='03')`。
- 有一个方便的小技巧是指定视图参数的默认值
- 每个捕获的参数都作为一个普通的Python 字符串传递给视图，无论正则表达式使用的是什么匹配方式

## URLconf在中搜索

请求的URL被看做是一个普通的Python 字符串， URLconf在其上查找并匹配。 进行匹配时将不包括GET或POST

`urlpatterns` 中的每个正则表达式在第一次访问它们时被编译。 这使得系统相当快。

## 包含其他URLconfs

```python
  url(r'^community/', include('django_website.aggregator.urls'))
```

这个例子中的正则表达式没有包含`$`（字符串结束匹配符），但是包含一个末尾的斜杠

另外一种包含其它URL 模式的方式是使用一个[`url()`](http://usyiyi.cn/documents/Django_111/ref/urls.html#django.conf.urls.url) 实例的列表

```python
from django.conf.urls import include, url

from apps.main import views as main_views
from credit import views as credit_views

extra_patterns = [
    url(r'^reports/$', credit_views.report),
    url(r'^reports/(?P<id>[0-9]+)/$', credit_views.report),
    url(r'^charge/$', credit_views.charge),
]

urlpatterns = [
    url(r'^$', main_views.homepage),
    url(r'^help/', include('apps.help.urls')),
    url(r'^credit/', include(extra_patterns)),
]
```

这可以用于移除URL配置中重复的部分

被包含的URLconf 会收到来自父URLconf 捕获的任何参数

## 嵌套参数？

正则表达式允许嵌套参数，Django 将解析它们并传递给视图。 当反查时，Django 将尝试填满所有外围捕获的参数，并忽略嵌套捕获的参数。 考虑下面的URL 模式，它带有一个可选的page 参数：

```
from django.conf.urls import url

urlpatterns = [
    url(r'blog/(page-(\d+)/)?$', blog_articles),                  # bad
    url(r'comments/(?:page-(?P<page_number>\d+)/)?$', comments),  # good
]

```

两个模式都使用嵌套的参数，其解析方式是：例如`blog/page-2/` 将匹配`page-2/`并带有两个位置参数`blog_articles` 和`2`。 第二个`comments` 的模式将匹配`page_number` 并带有一个值为2 的关键字参数`comments/page-2/`。 这个例子中外围参数是一个不捕获的参数`(?:...)`。

`blog_articles` 视图需要最外层捕获的参数来反查，在这个例子中是`comments`或者没有参数，而`page-2/`可以不带参数或者用一个`page_number`值来反查。

嵌套捕获的参数使得视图参数和URL 之间存在强耦合，正如`blog_articles` 所示：视图接收URL（`page-2/`）的一部分，而不只是视图所要的值。 这种耦合在反查时更加显著，因为反查视图时我们需要传递URL 的一个片段而不只是page 的值。

作为一个经验的法则，当正则表达式需要一个参数但视图忽略它的时候，只捕获视图需要的值并使用非捕获参数。

## 传递额外的选项来查看函数

URLconfs 具有一个钩子，让你传递一个Python 字典作为额外的参数传递给视图函数。

[`django.conf.urls.url()`](http://usyiyi.cn/documents/Django_111/ref/urls.html#django.conf.urls.url) 函数可以接收一个可选的第三个参数，它是一个字典，表示想要传递给视图函数的额外关键字参数。

像这样：

```
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^blog/(?P<year>[0-9]{4})/$', views.year_archive, {'foo': 'bar'}),
]

```

在这个例子中，对于`/blog/2005/`请求，Django 将调用`views.year_archive(request, year='2005', foo='bar')`。类似地，你可以传递额外的选项给[`include()`](http://usyiyi.cn/documents/Django_111/ref/urls.html#django.conf.urls.include)

## URL的反向解析

- 在模板中：使用[`url`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatetag-url) 模板标签。
- 在Python代码中：使用[`reverse()`](http://usyiyi.cn/documents/Django_111/ref/urlresolvers.html#django.urls.reverse)函数。
- 在更高层的与处理Django 模型实例相关的代码中：使用[`get_absolute_url()`](http://usyiyi.cn/documents/Django_111/ref/models/instances.html#django.db.models.Model.get_absolute_url) 方法。

在某些场景中，一个视图是通用的，所以在URL 和视图之间存在多对一的关系

当命名URL模式时，选择不太可能与其他应用程序的名称选择冲突的名称。 如果您调用您的URL模式`comment`，另一个应用程序执行相同的操作，那么[`reverse()`](http://usyiyi.cn/documents/Django_111/ref/urlresolvers.html#django.urls.reverse)查找的URL取决于项目的`urlpatterns`

在您的网址名称上放置一个前缀，可能源自应用程序名称（例如`myapp-comment`而不是`comment`）会减少碰撞的几率。

一个URL命名空间有两个部分，它们都是字符串：

- 应用命名空间

  它表示正在部署的应用的名称。 一个应用的每个实例具有相同的应用命名空间。 例如，可以预见Django 的管理站点的应用命名空间是`'admin'`。

- 实例命名空间

  它表示应用的一个特定的实例。 实例的命名空间在你的全部项目中应该是唯一的。 但是，一个实例的命名空间可以和应用的命名空间相同。 它用于表示一个应用的默认实例。 例如，Django 管理站点实例具有一个默认的实例命名空间`'admin'`。

URL 的命名空间使用`':'` 操作符指定。 例如，管理站点应用的主页使用`'admin:index'`。 它表示`'admin'` 的一个命名空间和`'index'` 的一个命名URL。

命名空间也可以嵌套。 命名URL`'sports:polls:index'` 将在命名空间`'polls'`中查找`'index'`，而poll 定义在顶层的命名空间`'sports'` 中。

# Django 中 app_name (应用命名空间) 和 namespace (实例命名空间) 的区别

应用命名空间
它表示正在部署的应用的名称。一个应用的每个实例具有相同的应用命名空间。例如，可以预见Django 的管理站点的应用命名空间是'admin'。

实例命名空间
它表示应用的一个特定的实例。实例的命名空间在你的全部项目中应该是唯一的。但是，一个实例的命名空间可以和应用的命名空间相同。它用于表示一个应用的默认实例。例如，Django 管理站点实例具有一个默认的实例命名空间'admin'。
URL 的命名空间使用':' 操作符指定。例如，管理站点应用的主页使用'admin:index'。它表示'admin' 的一个命名空间和'index' 的一个命名URL。

### 反转命名空间的URL ？

当解析一个带命名空间的URL（例如`'polls:index'`）时，Django 将切分名称为多个部分，然后按下面的步骤查找：

1. 首先，Django 查找匹配的[application namespace](http://usyiyi.cn/documents/Django_111/topics/http/urls.html#term-application-namespace)(在这个例子中为`'polls'`）。 这将得到该应用实例的一个列表。

2. 如果定义了当前的应用程序，Django会发现并返回该实例的URL解析器。 可以使用[`reverse()`](http://usyiyi.cn/documents/Django_111/ref/urlresolvers.html#django.urls.reverse)函数的`current_app`参数指定当前应用程序。

   [`url`](http://usyiyi.cn/documents/Django_111/ref/templates/builtins.html#std:templatetag-url)模板标签使用当前解析的视图的命名空间作为[`RequestContext`](http://usyiyi.cn/documents/Django_111/ref/templates/api.html#django.template.RequestContext)中的当前应用程序。 您可以通过在[`request.current_app`](http://usyiyi.cn/documents/Django_111/ref/request-response.html#django.http.HttpRequest.current_app)属性上设置当前应用程序来覆盖此默认值。

3. 如果没有当前应用。 Django 将查找一个默认的应用实例。 默认的应用实例是[instance namespace](http://usyiyi.cn/documents/Django_111/topics/http/urls.html#term-instance-namespace) 与[application namespace](http://usyiyi.cn/documents/Django_111/topics/http/urls.html#term-application-namespace) 一致的那个实例（在这个例子中，`polls` 的一个叫做`'polls'` 的实例）。

4. 如果没有默认的应用实例，Django 将挑选该应用最后部署的实例，不管实例的名称是什么。

5. 如果提供的命名空间与第1步中的[application namespace](http://usyiyi.cn/documents/Django_111/topics/http/urls.html#term-application-namespace) 不匹配，Django 将尝试直接将此命名空间作为一个[instance namespace](http://usyiyi.cn/documents/Django_111/topics/http/urls.html#term-instance-namespace)查找。

如果有嵌套的命名空间，将为命名空间的每个部分重复调用这些步骤直至剩下视图的名称还未解析。 然后该视图的名称将被解析到找到的这个命名空间中的一个URL。