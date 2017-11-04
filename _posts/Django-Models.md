---
title: Django模型
date: 2017-10-10 11:47:14
tags: 
- Django
- Python
---

# Django-Models

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```

```sql
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```

- 这个表的名称`myapp_person`，是根据模型中的某些元数据自动生成的，也可以重写为别的名称。 详见[表的名称](http://python.usyiyi.cn/documents/Django_111/ref/models/options.html#table-names)。
- `id` 字段是自动添加的，但这个行为可以被重写。 请参见[自动主键字段](http://python.usyiyi.cn/documents/Django_111/topics/db/models.html#automatic-primary-key-fields)。
- 这个例子中的`CREATE TABLE` SQL 语句使用PostgreSQL 语法格式，要注意的是Django 会根据[settings文件](http://python.usyiyi.cn/documents/Django_111/topics/settings.html) 中指定的数据库类型来使用相应的SQL 语句。

修改配置文件中的[`INSTALLED_APPS`](http://python.usyiyi.cn/documents/Django_111/ref/settings.html#std:setting-INSTALLED_APPS) 设置，在其中添加`models.py`所在应用的名称

```
INSTALLED_APPS = [
    #...
    'myapp',
    #...
]
```

### `AutoField`

- *class *`AutoField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#AutoField)

一个根据实际ID自动增长的[`IntegerField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.IntegerField) . 你通常不需要直接使用它；如果没有另外指定，主键字段将自动添加到您的模型中。 详细查看 [Automatic primary key fields](http://python.usyiyi.cn/documents/Django_111/topics/db/models.html#automatic-primary-key-fields).

### `BigAutoField`

- *class *`BigAutoField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#BigAutoField)


Django中的新功能1.10。

一个64位整数，非常像[`AutoField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.AutoField)，除了它保证适合`1`到`9223372036854775807`之间的数字。

### `BigIntegerField`

- *class *`BigIntegerField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#BigIntegerField)


一个64位整数，非常像[`IntegerField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.IntegerField)，除了它保证适合从`-9223372036854775808`到`9223372036854775807`的数字。 这个字段默认的表单组件是一个[`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput).

### `BinaryField`

- *class *`BinaryField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#BinaryField)


这是一个用来存储原始二进制码的Field. 只支持`bytes` 赋值， 注意这个Field只有很有限的功能。 例如，不大可能在一个`BinaryField` 值的数据上进行查询 在[`ModelForm`](http://python.usyiyi.cn/documents/Django_111/topics/forms/modelforms.html#django.forms.ModelForm)中也不可能包含`BinaryField`。

### `BooleanField`

- *class *`BooleanField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#BooleanField)


true/false 字段。

此字段的默认表单挂件是一个[`CheckboxInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.CheckboxInput).

如果你需要设置[`null`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.null) 值，则使用[`NullBooleanField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.NullBooleanField) 来代替BooleanField。

如果[`Field.default`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.default)没有指定的话， `BooleanField` 的默认值是 `None`。

### `CharField`

- *class *`CharField`(*max_length=None*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#CharField)


一个用来存储从小到很大各种长度的字符串的地方

如果是巨大的文本类型, 可以用 [`TextField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.TextField).

这个字段默认的表单组件是一个[`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput).

[`CharField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField)必须接收一个额外的参数:

- `CharField.``max_length`

  字段的最大字符长度. max_length将在数据库层和Django表单验证中起作用, 用来限定字段的长度.

### `DateField`

- *class *`DateField`(*auto_now=False*, *auto_now_add=False*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#DateField)


这是一个使用Python的`datetime.date`实例表示的日期. 有几个额外的设置参数:

- `DateField.``auto_now`

  每次保存对象时，自动设置该字段为当前时间。 用于"最后一次修改"的时间戳。 请注意，当前日期为*始终使用*它不只是一个可以覆盖的默认值。调用[`Model.save()`](http://python.usyiyi.cn/documents/Django_111/ref/models/instances.html#django.db.models.Model.save)时，该字段才会自动更新。 当以其他方式更新其他字段（例如[`QuerySet.update()`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet.update)）时，该字段不会更新，尽管您可以为此类型的更新指定字段的自定义值。


- `DateField.``auto_now_add`

  当对象第一次被创建时自动设置当前时间。 用于创建时间的时间戳. 请注意，当前日期为*始终使用*它不只是一个可以覆盖的默认值。 因此，即使您在创建对象时为此字段设置了一个值，也将被忽略。 如果您想要修改此字段，请设置以下代替`auto_now_add=True`：对于[`DateField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.DateField)：`default=date.today` - 从[`datetime.date.today()`](https://docs.python.org/3/library/datetime.html#datetime.date.today)对于[`DateTimeField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.DateTimeField)：`default=timezone.now` - 从[`django.utils.timezone.now()`](http://python.usyiyi.cn/documents/Django_111/ref/utils.html#django.utils.timezone.now)

这个字段默认的表单组件是一个[`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput). 在管理员站点添加了一个JavaScript写的日历控件，和一个“Today"的快捷按钮. 包含了一个额外的`invalid_date`错误消息键.

`default`, `auto_now`, and `auto_now_add` 这些设置是相互排斥的. 他们之间的任何组合将会发生错误的结果.

### `DateTimeField`

- *class *`DateTimeField`(*auto_now=False*, *auto_now_add=False*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#DateTimeField)


它是通过Python `datetime.datetime`实例表示的日期和时间. 携带了跟[`DateField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.DateField)一样的额外参数.

该字段默认对应的表单控件是一个单个的[`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput)(单文本输入框). 管理界面是使用两个带有 JavaScript控件的 [`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput) 文本框.

### `DecimalField`

- *class *`DecimalField`(*max_digits=None*, *decimal_places=None*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#DecimalField)


用python中 [`Decimal`](https://docs.python.org/3/library/decimal.html#decimal.Decimal) 的一个实例来表示十进制浮点数. 有两个 **必须的**参数:

- `DecimalField.``max_digits`

  位数总数，包括小数点后的位数。 该值必须大于等于`decimal_places`.


- `DecimalField.``decimal_places`

  小数点后的数字数量

### `DurationField`

- *class *`DurationField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#DurationField)


用作存储一段时间的字段类型 - 类似Python中的[`timedelta`](https://docs.python.org/3/library/datetime.html#datetime.timedelta). 当数据库使用的是PostgreSQL, 该数据类型使用的是一个 `interval` 而在Oracle上，则使用的是 `INTERVAL DAY(9) TO SECOND(6)`. 否则使用微秒的`bigint`。

### `EmailField`

- *class *`EmailField`(*max_length=254*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#EmailField)


一个 [`CharField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField) 用来检查输入的email地址是否合法。 它使用 [`EmailValidator`](http://python.usyiyi.cn/documents/Django_111/ref/validators.html#django.core.validators.EmailValidator) 来验证输入合法性。

### `FileField`

- *class *`FileField`(*upload_to=None*, *max_length=100*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields/files.html#FileField)


一个上传文件的字段。`primary_key`参数不受支持，如果使用，将引发错误。

`FileField.``upload_to`

该属性提供了一种设置上传目录和文件名的方式，可以通过两种方式进行设置。 在这两种情况下，值将传递到[`Storage.save()`](http://python.usyiyi.cn/documents/Django_111/ref/files/storage.html#django.core.files.storage.Storage.save)方法。

如果您指定一个字符串值，它可能包含[`strftime()`](https://docs.python.org/3/library/time.html#time.strftime)格式化，将被文件上传的日期/时间替换（以便上传的文件不填满给定的目录）。 像这样：

```
class MyModel(models.Model):
    # file will be uploaded to MEDIA_ROOT/uploads
    upload = models.FileField(upload_to='uploads/')
    # or...
    # file will be saved to MEDIA_ROOT/uploads/2015/01/30
    upload = models.FileField(upload_to='uploads/%Y/%m/%d/')
```

#### `FileField` 和 `FieldFile` 

- *class *`FieldFile`[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields/files.html#FieldFile)


当你添加 [`FileField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.FileField) 到你的模型中时, 你实际上会获得一个 [`FieldFile`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.fields.files.FieldFile)的实例来替代将要访问的文件。

[`FieldFile`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.fields.files.FieldFile)的API反映了[`File`](http://python.usyiyi.cn/documents/Django_111/ref/files/file.html#django.core.files.File)的API，其中一个不同之处在于： *由类包装的对象不一定是围绕Python的内置文件对象的包装。* Instead, it is a wrapper around the result of the [`Storage.open()`](http://python.usyiyi.cn/documents/Django_111/ref/files/storage.html#django.core.files.storage.Storage.open) method, which may be a [`File`](http://python.usyiyi.cn/documents/Django_111/ref/files/file.html#django.core.files.File) object, or it may be a custom storage’s implementation of the [`File`](http://python.usyiyi.cn/documents/Django_111/ref/files/file.html#django.core.files.File) API.

除了[`File`](http://python.usyiyi.cn/documents/Django_111/ref/files/file.html#django.core.files.File)继承的API，例如`read()`和`write()`，[`FieldFile`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.fields.files.FieldFile)包含几种方法可用于与底层文件交互：

### `FilePathField`

- *class *`FilePathField`(*path=None*, *match=None*, *recursive=False*, *max_length=100*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#FilePathField)


一个 [`CharField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField) ，内容只限于文件系统内特定目录下的文件名。 有三个参数, 其中第一个是 **必需的**:

- `FilePathField.``path`

  必填。 这个[`FilePathField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.FilePathField) 应该得到其选择的目录的绝对文件系统路径。 例如: `"/home/images"`.


- `FilePathField.``match`

  可选的. [`FilePathField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.FilePathField) 将会作为一个正则表达式来匹配文件名。 但请注意正则表达式将将被作用于基本文件名，而不是完整路径。例如： `“富。* \。TXT $”`，它将匹配一个名为`foo23.txt`但不是`bar.txt`或`foo23.png`的文件。


- `FilePathField.``recursive`

  可选的. `True` 或 `False`. 默认值是 `False`。 声明是否包含所有子目录的[`path`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.FilePathField.path)


- `FilePathField.``allow_files`

  可选的. `True` 或 `False`. 默认是`True`. 声明是否包含指定位置的文件。 该参数或[`allow_folders`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.FilePathField.allow_folders) 中必须有一个为 `True`.


- `FilePathField.``allow_folders`

  可选的. `True` 或 `False`. 默认值是 `False`。 声明是否包含指定位置的文件夹。 该参数或 [`allow_files`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.FilePathField.allow_files) 中必须有一个为 `True`.

当然，这些参数可以同时使用。

有一点需要提醒的是 [`match`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.FilePathField.match)只匹配基本文件名（base filename）, 而不是整个文件路径（full path）

### `FloatField`

- *class *`FloatField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#FloatField)


用Python的一个`float` 实例来表示一个浮点数.

当[`localize`](http://python.usyiyi.cn/documents/Django_111/ref/forms/fields.html#django.forms.Field.localize)为`False`或[`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput)时，该字段的默认表单小部件是[`NumberInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.NumberInput)。

### `ImageField`

- *class *`ImageField`(*upload_to=None*, *height_field=None*, *width_field=None*, *max_length=100*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields/files.html#ImageField)


继承[`FileField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.FileField)的所有属性和方法，但还对上传的对象进行校验，确保它是个有效的图片。

除了从[`FileField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.FileField)继承来的属性外，[`ImageField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ImageField) 还有`height`和 `width`属性。

### `IntegerField`

- *class *`IntegerField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#IntegerField)


一个整数。 在Django所支持的所有数据库中，从 `-2147483648` 到 `2147483647` 范围内的值是合法的。 当[`localize`](http://python.usyiyi.cn/documents/Django_111/ref/forms/fields.html#django.forms.Field.localize)为`False`或[`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput)时，该字段的默认表单小部件是[`NumberInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.NumberInput)。

### `GenericIPAddressField`

- *class *`GenericIPAddressField`(*protocol='both'*, *unpack_ipv4=False*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#GenericIPAddressField)


一个 IPv4 或 IPv6 地址, 字符串格式 (例如 `192.0.2.30` 或 `2a02:42fe::4`). 这个字段默认的表单组件是一个[`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput).

### `NullBooleanField`

- *class *`NullBooleanField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#NullBooleanField)


类似[`BooleanField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.BooleanField), 但是允许 `NULL` 作为一个选项. 使用此代替`null=True`的[`BooleanField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.BooleanField)。 此字段的默认表单widget为[`NullBooleanSelect`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.NullBooleanSelect)。

### `PositiveIntegerField`

- *class *`PositiveIntegerField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#PositiveIntegerField)


类似 [`IntegerField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.IntegerField), 但值必须是正数或者零(`0`). 从`0`到`2147483647`的值在Django支持的所有数据库中都是安全的。 由于向后兼容性原因，接受值`0`。

### `PositiveSmallIntegerField`

- *class *`PositiveSmallIntegerField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#PositiveSmallIntegerField)


该模型字段类似 [`PositiveIntegerField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.PositiveIntegerField), 但是只允许小于某一特定值（依据数据库类型而定）。 从`0` 到 `32767` 这个区间，对于Django所支持的所有数据库而言都是安全的。

### `SlugField`

- *class *`SlugField`(*max_length=50*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#SlugField)


[Slug](http://python.usyiyi.cn/documents/Django_111/glossary.html#term-slug) 是一个新闻术语（通常叫做短标题）。 一个slug只能包含字母、数字、下划线或者是连字符，通常用来作为短标签。 通常它们是用来放在URL里的。

### `SmallIntegerField`

- *class *`SmallIntegerField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#SmallIntegerField)


Like an IntegerField, but only allows values under a certain (database-dependent) point. [`IntegerField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.IntegerField) 对于django来讲，该字段值在 `-32768` 至 `32767`这个范围内对所有可支持的数据库都是安全的。

### `TextField`

- *class *`TextField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#TextField)


大文本字段。 该模型默认的表单组件是[`Textarea`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.Textarea)。

如果你在这个字段类型中使用了`max_length`属性，它将会在渲染页面表单元素[`Textarea`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.Textarea) 时候体现出来。 但是并不会在model或者数据库级别强制性的限定字段长度。 请使用[`CharField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField)。

### `TimeField`

- *class *`TimeField`(*auto_now=False*, *auto_now_add=False*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#TimeField)


时间字段，和Python中 `datetime.time` 一样。 接受与[`DateField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.DateField)相同的自动填充选项。

这个字段默认的表单组件是一个[`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput). Admin添加一些JavaScript快捷方式。

### `URLField`

- *class *`URLField`(*max_length=200*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#URLField)


一个[`CharField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField) 类型的URL

这个字段默认的表单组件是一个[`TextInput`](http://python.usyiyi.cn/documents/Django_111/ref/forms/widgets.html#django.forms.TextInput).

与所有[`CharField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField)子类一样，[`URLField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.URLField)接受可选的[`max_length`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField.max_length)参数。 如果不指定[`max_length`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField.max_length)，则使用默认值200。

### `UUIDField`

- *class *`UUIDField`(**\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields.html#UUIDField)


一个用来存储UUID的字段。 使用Python的[`UUID`](https://docs.python.org/3/library/uuid.html#uuid.UUID)类。 当使用PostgreSQL数据库时，该字段类型对应的数据库中的数据类型是`uuid`，使用其他数据库时，数据库对应的是`char(32)`类型。

## 关系字段

Django 同样定义了一系列的字段来描述数据库之间的关联。

### `ForeignKey`

- *class *`ForeignKey`(*to*, *on_delete*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields/related.html#ForeignKey)


多对一关系。 需要两个位置参数：模型相关的类和[`on_delete`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey.on_delete)选项。 （`on_delete`实际上并不是必需的，但不提供它会给出不赞成的警告。 在Django 2.0中将需要它。）on_delete=models.CASCADE

如果你需要关联到一个还没有定义的模型，你可以使用模型的名字而不用模型对象本身.若要引用在其它应用中定义的模型，你可以用带有完整标签名的模型来显式指定

`ForeignKey` 会自动创建数据库索引。 你可以通过设置[`db_index`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.db_index) 为`False` 来取消

[`on_delete`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey.on_delete) 在[`django.db.models`](http://python.usyiyi.cn/documents/Django_111/topics/db/models.html#module-django.db.models)中可以找到的值有：

- - `CASCADE`[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/deletion.html#CASCADE)

    级联删除。 Django模拟SQL约束ON DELETE CASCADE的行为，并删除包含ForeignKey的对象。

- - `PROTECT`[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/deletion.html#PROTECT)

    抛出[`ProtectedError`](http://python.usyiyi.cn/documents/Django_111/ref/exceptions.html#django.db.models.ProtectedError) 以阻止被引用对象的删除，它是[`django.db.IntegrityError`](http://python.usyiyi.cn/documents/Django_111/ref/exceptions.html#django.db.IntegrityError) 的一个子类。

- - `SET_NULL`[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/deletion.html#SET_NULL)

    设置[`ForeignKey`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey) null；只有[`null`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.null)是`True`才有可能。

- - `SET_DEFAULT`[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/deletion.html#SET_DEFAULT)

    将[`ForeignKey`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey)设置为其默认值；必须设置[`ForeignKey`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey)的默认值。

- - `SET`()[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/deletion.html#SET)

    设置[`ForeignKey`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey) 为传递给[`SET()`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.SET) 的值，如果传递的是一个可调用对象，则为调用后的结果。 在大部分情形下，传递一个可调用对象用于避免models.py 在导入时执行查询：

    ```python
    from django.conf import settings
    from django.contrib.auth import get_user_model
    from django.db import models
    def get_sentinel_user():
    	return get_user_model().objects.get_or_create(username='deleted')[0]
    class MyModel(models.Model):
    	user = models.ForeignKey(
        	settings.AUTH_USER_MODEL,        			  		  							on_delete=models.SET(get_sentinel_user),    
        )
    ```

- - `DO_NOTHING`[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/deletion.html#DO_NOTHING)

    不采取任何动作。 如果您的数据库后端强制引用完整性，除非手动添加SQL `ON DELETE`约束，否则将导致[`IntegrityError`](http://python.usyiyi.cn/documents/Django_111/ref/exceptions.html#django.db.IntegrityError)到数据库字段。

`ForeignKey.limit_choices_to`

当这个字段使用`ModelForm`或者Admin 渲染时（默认情况下，查询集中的所有对象都可以使用），为这个字段设置一个可用的选项。它可以是一个字典、一个[`Q`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.Q) 对象或者一个返回字典或[`Q`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.Q)对象的可调用对象。

### `ManyToManyField`

- *class *`ManyToManyField`(*to*, **\*options*)[[source\]](http://python.usyiyi.cn/documents/Django_111/_modules/django/db/models/fields/related.html#ManyToManyField)


一个多对多关联。 要求一个关键字参数：与该模型关联的类，与[`ForeignKey`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey) 的工作方式完全一样，包括[recursive](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#recursive-relationships) 和[lazy](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#lazy-relationships)。

关联的对象可以通过字段的[`RelatedManager`](http://python.usyiyi.cn/documents/Django_111/ref/models/relations.html#django.db.models.fields.related.RelatedManager) 添加、删除和创建。



### 字段选项

每个字段都接受一组与字段有关的参数（文档在[模型字段参考](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#model-field-types)中）。 例如，[`CharField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField)（和它的派生类）需要[`max_length`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.CharField.max_length) 参数来指定`VARCHAR` 数据库字段的大小。

还有一些适用于所有字段的通用参数。 这些参数在[参考](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#common-model-field-options)中有详细定义，这里我们只简单介绍一些最常用的：

- [`null`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.null)

  如果为`True`，Django 将会把数据库中空值保存为`NULL`。 默认为`False`。

- [`blank`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.blank)

  如果为`True`，该字段允许为空值， 默认为`False`。要注意，这与 [`null`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.null) 不同。 [`null`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.null)纯粹是数据库范畴,指数据库中字段内容是否允许为空，而 [`blank`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.blank) 是表单数据输入验证范畴的。 如果一个字段的[`blank=True`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.blank)，表单的验证将允许输入一个空值。 如果字段的[`blank=False`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.blank)，该字段就是必填的。

- [`choices`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.choices)

  由二项元组构成的一个可迭代对象（例如，列表或元组），用来给字段提供选择项。 如果设置了choices ，默认的表单将是一个选择框而不是标准的文本框，而且这个选择框的选项就是choices 中的选项。这是一个关于 choices 列表的例子：

```python
YEAR_IN_SCHOOL_CHOICES = (
  ('FR', 'Freshman'),   
  ('SO', 'Sophomore'),    
  ('JR', 'Junior'),    
  ('SR', 'Senior'),   
  ('GR', 'Graduate'),
)
```

每个元组中的第一个元素是将被存储在数据库中的值。 第二个元素将由默认窗体小部件或[`ModelChoiceField`](http://python.usyiyi.cn/documents/Django_111/ref/forms/fields.html#django.forms.ModelChoiceField)显示。 给定一个模型实例，可以使用`get_FOO_display()`方法来访问选项字段的显示值

[`default`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.default)

字段的默认值。 可以是一个值或者可调用对象。 如果可调用 ，每个新对象被创建它都会被调用。

[`help_text`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.help_text)

表单部件额外显示的帮助内容。 即使字段不在表单中使用，它对生成文档也很有用。

[`primary_key`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.primary_key)

如果为`True`，那么这个字段就是模型的主键。主键字段是只读的。 如果你在一个已存在的对象上面更改主键的值并且保存，一个新的对象将会在原有对象之外创建出来

 [`unique`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.Field.unique)

如果为`True`, 则这个字段在整张表中必须是唯一的。

默认情况下，Django 会给每个模型添加下面这个字段：

```
id = models.AutoField(primary_key=True)

```

这是一个自增主键字段。



```
from django.db import models

class Topping(models.Model):
    # ...
    pass

class Pizza(models.Model):
    # ...
    toppings = models.ManyToManyField(Topping)
```

在哪个模型中设置 [`ManyToManyField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ManyToManyField) 并不重要，在两个模型中任选一个即可

[`OneToOneField`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.OneToOneField)用来定义一对一关系。 和使用其它`Field`类型一样：在模型当中把它做为一个类属性包含进来。



## `Meta`选项

使用内部的`class Meta` 定义模型的元数据，例如：

```
from django.db import models

class Ox(models.Model):
    horn_length = models.IntegerField()

    class Meta:
        ordering = ["horn_length"]
        verbose_name_plural = "oxen"
```

模型元数据是“任何不是字段的数据”，比如排序选项（[`ordering`](http://python.usyiyi.cn/documents/Django_111/ref/models/options.html#django.db.models.Options.ordering)），数据库表名（[`db_table`](http://python.usyiyi.cn/documents/Django_111/ref/models/options.html#django.db.models.Options.db_table)）或者人类可读的单复数名称（[`verbose_name`](http://python.usyiyi.cn/documents/Django_111/ref/models/options.html#django.db.models.Options.verbose_name) 和[`verbose_name_plural`](http://python.usyiyi.cn/documents/Django_111/ref/models/options.html#django.db.models.Options.verbose_name_plural)）。 在模型中添加`class Meta`是完全可选的，所有选项都不是必须的

在Django 中有3种风格的继承。

1. 通常，你只想使用父类来持有一些信息，你不想在每个子模型中都敲一遍。 这个类永远不会单独使用，所以你要使用[抽象的基类](http://python.usyiyi.cn/documents/Django_111/topics/db/models.html#abstract-base-classes)。
2. 如果你继承一个已经存在的模型且想让每个模型具有它自己的数据库表，那么应该使用[多表继承](http://python.usyiyi.cn/documents/Django_111/topics/db/models.html#multi-table-inheritance)。
3. 最后，如果你只是想改变一个模块Python 级别的行为，而不用修改模型的字段，你可以使用[代理模型](http://python.usyiyi.cn/documents/Django_111/topics/db/models.html#proxy-models)。

要保存对数据库中已存在的对象的改动，请使用[`save()`](http://python.usyiyi.cn/documents/Django_111/ref/models/instances.html#django.db.models.Model.save)。

更新[`ForeignKey`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey) 字段的方式和保存普通字段相同 — 只要把一个正确类型的对象赋值给该字段。

## 检索对象

通过模型中的[`Manager`](http://python.usyiyi.cn/documents/Django_111/topics/db/managers.html#django.db.models.Manager)构造一个[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)，来从你的数据库中获取对象。每个模型都至少有一个[`Manager`](http://python.usyiyi.cn/documents/Django_111/topics/db/managers.html#django.db.models.Manager)，它默认命名为[`objects`](http://python.usyiyi.cn/documents/Django_111/ref/models/class.html#django.db.models.Model.objects)

[`all()`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet.all) 方法返回了一个包含数据库表中所有记录[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)。 但在通常情况下，你往往想要获取的是完整数据集的一个子集。

要创建这样一个子集，你需要在原始的的[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)上增加一些过滤条件。 [`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)两个最普遍的途径是：

- `filter(**kwargs)`

  返回一个新的[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)，它包含满足查询参数的对象。

- `exclude(**kwargs)`

  返回一个新的[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)，它包含*不*满足查询参数的对象。

[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)的筛选结果本身还是[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)，所以可以将筛选语句链接在一起

`QuerySets` 是惰性执行的 —— 创建[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)不会带来任何数据库的访问。 你可以将过滤器保持一整天，直到[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet) 需要*求值*时，Django 才会真正运行这个查询。

### 使用`get()`检索单个对象[¶](http://python.usyiyi.cn/documents/Django_111/topics/db/queries.html#retrieving-a-single-object-with-get)

[`filter()`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet.filter) 始终给你一个[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)，即使只有一个对象满足查询条件 —— 这种情况下，[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)将只包含一个元素。

### 限制`QuerySet`

可以使用Python 的切片语法来限制[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)记录的数目 。 它等同于SQL 的`OFFSET` 和`LIMIT` 子句。

数据库API支持大约二十种查找类型

[`exact`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#std:fieldlookup-exact)

[`iexact`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#std:fieldlookup-iexact)

[`contains`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#std:fieldlookup-contains)

[`icontains`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#std:fieldlookup-icontains)

gt

```python
Entry.objects.get(headline__contains='Lennon')
```

想将模型的一个字段与同一个模型的另外一个字段进行比较.Django 提供[`F表达式`](http://python.usyiyi.cn/documents/Django_111/ref/models/expressions.html#django.db.models.F) 来允许这样的比较。 `F()` 返回的实例用作查询内部对模型字段的引用

### 缓存和`QuerySet`

在一个新创建的[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)中，缓存为空。 首次对[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)进行求值 —— 同时发生数据库查询 ——Django 将保存查询的结果到[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)的缓存中并返回明确请求的结果（例如，如果正在迭代[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)，则返回下一个结果）。 接下来对该[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet) 的求值将重用缓存的结果。删除方法，为了方便，就取名为[`delete()`](http://python.usyiyi.cn/documents/Django_111/ref/models/instances.html#django.db.models.Model.delete)你还可以批量删除对象。 每个[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet) 都有一个[`delete()`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet.delete) 方法，它将删除该[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)中的所有成员有时你想为一个[`QuerySet`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet)中所有对象的某个字段都设置一个特定的值。 这时你可以使用[`update()`](http://python.usyiyi.cn/documents/Django_111/ref/models/querysets.html#django.db.models.query.QuerySet.update) 方法。 像这样：

```
# Update all the headlines with pub_date in 2007.
Entry.objects.filter(pub_date__year=2007).update(headline='Everything is the same')
```

#### 反向查询

如果模型有一个[`ForeignKey`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey)，那么该ForeignKey所指的模型实例可以通过一个[`Manager`](http://python.usyiyi.cn/documents/Django_111/topics/db/managers.html#django.db.models.Manager)返回第一个模型的所有实例。 默认情况下，这个[`Manager`](http://python.usyiyi.cn/documents/Django_111/topics/db/managers.html#django.db.models.Manager)的名字为`FOO_set`，其中`FOO`是源模型的小写名称。

#### 前向查询

如果一个模型具有[`ForeignKey`](http://python.usyiyi.cn/documents/Django_111/ref/models/fields.html#django.db.models.ForeignKey)，那么该模型的实例将可以通过属性访问关联的（外部）对象。

