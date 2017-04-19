---
title: 设计模式系列(2) 观察者模式
categories: 设计模式
---
# 引入目的
建立一种对象与对象之间的依赖关系，一个对象发生改变时将自动通知其他对象，其他对象将相应做出反应。一个观察目标可以对应多个观察者，而且这些观察者之间没有相互联系，可以根据需要增加和删除观察者，使得系统更易于扩展
<!--more-->
# 实现
一般实现有两种方式
* push模式
* pull模式

在Java中提供了基本的接口可供继承实现
## Observable
```java
class Watched extends Observable {

}
```

## Observer
```java
class Watcher 

```
