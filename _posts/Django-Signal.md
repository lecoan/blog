---
title: Django中的Signal
date: 2017-10-10 11:47:14
tags: 
- Django
- Python
---

# Django - Signal

Django 提供一个“信号分发器”，允许解耦的应用在框架的其它地方发生操作时会被通知到

Django 提供[set of built-in signals](http://usyiyi.cn/documents/Django_111/ref/signals.html)，允许用户的代码获得Django 特定操作的通知。 这包含一些有用的通知：

- [`django.db.models.signals.pre_save`](http://usyiyi.cn/documents/Django_111/ref/signals.html#django.db.models.signals.pre_save)＆[`django.db.models.signals.post_save`](http://usyiyi.cn/documents/Django_111/ref/signals.html#django.db.models.signals.post_save)

  在模型 [`save()`](http://usyiyi.cn/documents/Django_111/ref/models/instances.html#django.db.models.Model.save)方法调用之前或之后发送。

- [`django.db.models.signals.pre_delete`](http://usyiyi.cn/documents/Django_111/ref/signals.html#django.db.models.signals.pre_delete)＆[`django.db.models.signals.post_delete`](http://usyiyi.cn/documents/Django_111/ref/signals.html#django.db.models.signals.post_delete)

  在模型[`delete()`](http://usyiyi.cn/documents/Django_111/ref/models/instances.html#django.db.models.Model.delete)方法或查询集的[`delete()`](http://usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet.delete) 方法调用之前或之后发送。

- [`django.db.models.signals.m2m_changed`](http://usyiyi.cn/documents/Django_111/ref/signals.html#django.db.models.signals.m2m_changed)

  模型上的 [`ManyToManyField`](http://usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ManyToManyField) 修改时发送。

- [`django.core.signals.request_started`](http://usyiyi.cn/documents/Django_111/ref/signals.html#django.core.signals.request_started)＆[`django.core.signals.request_finished`](http://usyiyi.cn/documents/Django_111/ref/signals.html#django.core.signals.request_finished)

  Django建立或关闭HTTP 请求时发送。

## 监听Signal

要接收信号，请使用[`Signal.connect()`](http://usyiyi.cn/documents/Django_111/topics/signals.html#django.dispatch.Signal.connect)方法注册

### 定义接受函数

首先，我们需要定义接收器函数。 接受器可以是Python函数或者方法：

```python
def my_callback(sender, **kwargs):
    print("Request finished!")
```

请注意，该函数采用`sender`参数，以及通配符关键字参数（`**kwargs`）；所有信号处理程序必须采取这些参数。

* **kwargs 

  所有信号都发送关键字参数，并且可以在任何时候修改这些关键字参数。信号在任何时候都可能添加参数，你的receiver 必须能够处理这些新的参数。

* sender

  一些信号会发送多次，但是你只想接收这些信号的一个确定的子集。在这些情况下，你可以通过注册来接收只由特定sender 发出的信号。

  ```python
  from django.db.models.signals import pre_save
  from django.dispatch import receiver
  from myapp.models import MyModel


  @receiver(pre_save, sender=MyModel)
  def my_handler(sender, **kwargs):
      pass
  ```

### 建立连接

手动连接

```python
from django.core.signals import request_finished

request_finished.connect(my_callback)
```

采用装饰器连接

```python
from django.core.signals import request_finished
from django.dispatch import receiver

@receiver(request_finished)
def my_callback(sender, **kwargs):
    print("Request finished!")
```

严格来说，信号处理和注册的代码应该放在你想要的任何地方，但是推荐避免放在应用的根模块和`models`模块中，以尽量减少产生导入代码的副作用。

实际上，信号处理通常定义在应用相关的`signals`子模块中。 信号receivers 在你应用配置类中的[`ready()`](http://usyiyi.cn/documents/Django_111/ref/applications.html#django.apps.AppConfig.ready) 方法中连接。 如果你使用[`receiver()`](http://usyiyi.cn/documents/Django_111/topics/signals.html#django.dispatch.receiver) 装饰器，只需要在[`ready()`](http://usyiyi.cn/documents/Django_111/ref/applications.html#django.apps.AppConfig.ready) 内部导入`signals`子模块就可以。

### 防止重复信号

可以传递一个唯一的标识符作为`dispatch_uid` 参数来标识你的receiver 函数。 标识符通常是一个字符串，虽然任何可计算哈希的对象都可以。 最后的结果是，对于每个唯一的`dispatch_uid`值，你的receiver 函数都只绑定到信号一次：

```python
from django.core.signals import request_finished

request_finished.connect(my_callback, dispatch_uid="my_unique_identifier")
```

## 定义和发送Signal

### 声明信号

所有信号都是 [`django.dispatch.Signal`](http://usyiyi.cn/documents/Django_111/topics/signals.html#django.dispatch.Signal) 的实例。 `providing_args`是一个列表，由信号将提供给监听者的参数名称组成。 理论上是这样，但是实际上并没有任何检查来保证向监听者提供了这些参数。

```python
import django.dispatch

pizza_done = django.dispatch.Signal(providing_args=["toppings", "size"])
```

这段代码声明了`pizza_done`信号，它向接受者提供`size`和 `toppings` 参数。

要记住你可以在任何时候修改参数的列表，所以首次尝试的时候不需要完全确定API。

### 发送

要发送信号，请调用[`Signal.send()`](http://usyiyi.cn/documents/Django_111/topics/signals.html#django.dispatch.Signal.send)（所有内置信号都使用此信号）或[`Signal.send_robust()`](http://usyiyi.cn/documents/Django_111/topics/signals.html#django.dispatch.Signal.send_robust)。 您必须提供`sender`参数（大部分时间是一个类），并且可以提供任意数量的其他关键字参数。

例如，这样来发送我们的`pizza_done`信号：

```python
class PizzaStore(object):

    def send_pizza(self, toppings, size):
        pizza_done.send(sender=self.__class__, toppings=toppings, size=size)
```

### 断开

调用[`Signal.disconnect()`](http://usyiyi.cn/documents/Django_111/topics/signals.html#django.dispatch.Signal.disconnect)来断开信号的接收器。 [`Signal.connect()`](http://usyiyi.cn/documents/Django_111/topics/signals.html#django.dispatch.Signal.connect)中描述了所有参数。 如果接收器成功断开，返回 `False` ，否则返回`True`。

`receiver` 参数表示要断开的已注册receiver。 如果使用`dispatch_uid` 标识receiver，它可以为`None`







