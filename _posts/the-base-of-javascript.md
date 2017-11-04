---
TAG:
---

# JavaScript基础

## 简介

JavaScript是一门轻量级的、动态的、弱类型的脚本语言，非常适合面向对象和函数式的编程风格

JavaScript被设计用来向HTML页面添加**交互行为**

>- html定义网页的内容
>- css定义网页的布局外观
>- js定义网页的行为

### 结合HTML

* HTML 中的脚本必须位于<script> 与 </script>标签之间。
* 脚本可被放置在 HTML 页面的 <body>和 <head>部分中。
* HTML中引入外部Javascript文件需在 <script>标签的 “src” 属性中设置该 .js文件
* 外部脚本不能包含 <script> 标签

## 语法

### 数据类型

JavaScript数据类型分为两类：原始类型和对象类型

* 原始类型
  * 数字、文本和布尔值
  * 两个特殊的原始值：null 和undefined
* 对象类型
  * 对象是**属性的集合**，每个属性都有key-value对组成，value可以是原始值也可以是对象
  * 普通的js对象是“key-value”对的无序集合，js还定义了特殊的对象---数组，即有序集合
  * js还定义了一种特殊的对象函数

> 不区分整型浮点型，所有数字采用浮点数值表示, 还可通过Math对象进行复杂运算
>
> 任意JavaScript的值都可以转换为布尔值
>
> 布尔值有`toString`方法

#### 两种特殊的类型

* null

  用来描述空值

  typeof（null）返回“object”，说明可认为null是一个特殊的对象

* undefined

  未定义，typeof(undefined)返回"undefined“

> null== undefined 为true
>
> null=== undefined 为false

#### 类型转换

* JS支持灵活的自动类型转换

  如：10+”objects”

  “7”*”4”

* 显式类型转换，让代码更清晰易读

  Number(“3”)

  x + “”    等价于 String(x)

  !!x  等价于Boolean(x)

#### 对象

* 对象是一种复合值，它将很多值聚合在一起，对象可看做属性的无序集合，属性是一个key-value对，属性名是字符串
* 对象是动态的，可以新增和删除属性
* 对象还可从原型对象继承属性，对象的方法通常是继承的属性

### 变量

变量使用前要先声明，使用var关键字声明

* 局部变量：在函数内声明，只能在函数内部访问
* 全局变量：在函数外定义，网页中所有脚本和函数均可使用

> 全局变量是全局对象的属性，var声明的全局变量无法通过delete删除

**比较运算   相等：“==”可进行类型转换  严格相等：“===”**

## 操作DOM

### DOM介绍

文档对象模型（DOM） 是中立于平台和语言的接口，它允许程序和脚本动态地访问和更新文档的内容、结构和样式, 是W3C（万维网联盟）的标准

* 当网页被加载时，浏览器会创建页面的文档对象模型DOM
* 所有的HTML元素都是元素节点
* 所有HTML 属性都是属性节点
* 文本插入到HTML 元素是文本节点
* 注释是注释节点
* 节点树中的节点彼此拥有层级关系：父（parent）、子（child）和同胞（sibling）

### JS操作DOM

一些常用的 HTMLDOM 方法：

* getElementById(id)- 获取带有指定id 的节点（元素）
* appendChild(node)- 插入新的子节点（元素）
* removeChild(node)- 删除子节点（元素）

一些常用的 HTMLDOM 属性：

* innerHTML- 节点（元素）的文本值
* parentNode- 节点（元素）的父节点
* childNodes- 节点（元素）的子节点
* attributes- 节点（元素）的属性节点

### 元素对象

* 在 HTMLDOM 中, 元素对象代表着一个HTML 元素。
* 元素对象 的 子节点可以是元素节点、文本节点、注释节点。
* NodeList对象 代表了节点列表，类似于HTML元素的子节点集合。

### 属性对象

* HTML属性总是属于HTML元素

### 事件对象

* HTMLDOM 事件允许Javascript在HTML文档元素中注册不同事件处理程序。
* 鼠标事件、键盘事件、框架/对象事件
* 表单事件、剪贴板事件、打印事件
* 拖动事件、多媒体（Media）事件
* 动画事件、过渡事件、其它事件

### document对象

当浏览器载入 HTML 文档,它就会成为 document对象。

document对象是HTML文档的根节点与所有其他节点（元素节点，文本节点，属性节点,注释节点）。

document对象使我们可以从脚本中对HTML 页面中的所有元素进行访问。

> Document对象属性和方法示例：
>
> * [document.title](http://www.runoob.com/jsref/prop-doc-title.html)
> * [document.](http://www.runoob.com/jsref/met-document-getelementsbyclassname.html)[getElementsByClassName](http://www.runoob.com/jsref/met-document-getelementsbyclassname.html)[()](http://www.runoob.com/jsref/met-document-getelementsbyclassname.html)
> * [document.getElementById](http://www.runoob.com/jsref/met-document-getelementbyid.html)[()](http://www.runoob.com/jsref/met-document-getelementbyid.html)
> * [document.getElementsByName](http://www.runoob.com/jsref/met-doc-getelementsbyname.html)[()](http://www.runoob.com/jsref/met-doc-getelementsbyname.html)

### Window对象

* 所有浏览器都支持 window对象。它表示浏览器窗口。
* 所有 JavaScript全局对象、函数以及变量均自动成为window 对象的成员。
* 全局变量是 window对象的属性。
* 全局函数是 window对象的方法。
* 甚至 HTMLDOM 的document 也是 window对象的属性之一

相关函数

* window.screen 对象包含有关用户屏幕的信息
* window.location 获得当前界面的地址(URL)，并把浏览器重定向到新的页面
* window.history 包含浏览器的历史
* window.navigator 浏览器信息



* alert() confirm() prompt()
* setInterval() setTimeout()
* document.cookie

## jQuery

### 功能

* HTML 元素选取与操作
* CSS 操作
* HTML 事件函数
* JavaScript特效和动画
* HTMLDOM 遍历和修改
* AJAX  以及其它Utilities

### 语法

jQuery语法是通过选取HTML 元素，并对选取的元素执行某些操作。

* 基础语法： $(selector).action()
  * 美元符号定义 jQuery
  * 选择符（selector）"查询"和"查找"HTML 元素
  * jQuery的 action()执行对元素的操作

为了防止文档在完全加载（就绪）之前运行 jQuery代码，所有jQuery 函数需位于一个document ready 函数中

```javascript
}$(document).ready(function(){
   // jQuery methods go here...
});
```

#### 选择器

```javascript
$(document).ready(function(){
  $("button").click(function(){
    $("p").hide();
  });
});
```



