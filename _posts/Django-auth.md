# Django认证系统

## User对象

[`User`](http://usyiyi.cn/documents/Django_111/ref/contrib/auth.html#django.contrib.auth.models.User)对象是认证系统的核心。 它们通常表示与你的站点进行交互的用户，并用于启用限制访问、注册用户信息和给创建者关联内容等。 在Django的认证框架中只存在一种类型的用户，因此诸如[`'superusers'`](http://usyiyi.cn/documents/Django_111/ref/contrib/auth.html#django.contrib.auth.models.User.is_superuser)或管理员[`'staff'`](http://usyiyi.cn/documents/Django_111/ref/contrib/auth.html#django.contrib.auth.models.User.is_staff)用户只是具有特殊属性集的user对象，而不是不同类型的user对象。

默认user的基本属性有：

- username
- password
- email
- first_name
- last_name

### 创建user

创建users最直接的方法是使用[`create_user()`](http://usyiyi.cn/documents/Django_111/ref/contrib/auth.html#django.contrib.auth.models.UserManager.create_user)辅助函数：

```python
from django.contrib.auth.models import User
 user = 用户.objects.create_user('john', 'lennon@thebeatles.com', 'johnpassword')

# At this point, user is a User object that has already been saved
# to the database. You can continue to change its attributes
# if you want to change other fields.
user.姓 = 'Lennon'
user.save()
```

### 创建superuser

使用`createsuperuser`命令创建superusers：

```shell
python manage.py createsuperuser --username=joe --email=joe@example.com
```

### 更改密码

Django不会在user模型上存储原始的（明文）密码，而只是一个哈希，不要尝试直接操作user的password属性。

若要修改一个用户的密码，你有几种选择：

`manage.py changepassword <username>`提供了一种从命令行更改用户密码的方法。 它提示你修改一个给定user的密码，你必须输入两次。 如果它们匹配，新的密码将会立即修改。 如果你没有提供user，命令行将尝试修改与当前系统用户匹配的用户名的密码。

你也可以通过程序修改密码，使用[`set_password()`](http://usyiyi.cn/documents/Django_111/ref/contrib/auth.html#django.contrib.auth.models.User.set_password)：

```python
from django.contrib.auth.models import User
u = User.objects.get(username='john')
u.set_password('new password')
u.save()
```

### 认证

使用`authenticate()`来验证一组凭据

```python
from django.contrib.auth import authenticate
user = authenticate(username='john', password='secret')
if user is not None:
    # A backend authenticated the credentials
else:
    # No backend authenticated the credentials
```

## 权限与授权

Django本身提供了一个简单的权限系统。 它提供一种分配权限给特定的用户和用户组的方法

Django admin 站点使用如下的权限：

- 拥有该类型对象"add"权限的用户才可以访问"add"表单以及添加一个该类型对象。
- 查看修改列表、查看“change”表单以及修改一个对象的权利只限于具有该类型对象的“change”权限的用户拥有。
- 用户必须在一个对象上具有“delete”权限，才能删除这个对象。

[`User`](http://usyiyi.cn/documents/Django_111/ref/contrib/auth.html#django.contrib.auth.models.User)对象具有两个多对多的字段：`user_permissions`和`groups`。 [`User`](http://usyiyi.cn/documents/Django_111/ref/contrib/auth.html#django.contrib.auth.models.User)对象可以用和其它[Django model](http://usyiyi.cn/documents/Django_111/topics/db/models.html)一样的方式访问它们相关的对象：

```python
myuser.groups.set([group_list])
myuser.groups.add(group, group, ...)
myuser.groups.remove(group, group, ...)
myuser.groups.clear()
myuser.user_permissions.set([permission_list])
myuser.user_permissions.add(permission, permission, ...)
myuser.user_permissions.remove(permission, permission, ...)
myuser.user_permissions.clear()
```

