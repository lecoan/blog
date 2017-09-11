---
title: saveInstance详解
date: 2017-08-10 11:20:16
tags: Android
---

# oncreate()参数saveInstanceState详解

saveInstanceState这个一定不陌生，这个作为参数出现在onCreate()方法里。但是大多数情况下都用不到这个。实际上这个参数是为了重新启动应用储存数据存在的，跟她相关的还有两个方法：onSaveInstanceState() onRestoreInstanceState()

## 什么时候调用这两个方法？

在android里的官方文档是这样写的

> 当您的Activity因用户按了返回 或Activity自行完成而被销毁时，系统的     Activity 实例概念将永久消失，因为行为指示不再需要Activity。           但是，如果系统因系统局限性（而非正常应用行为）而销毁Activity，尽管 Activity 实际实例已不在，系统会记住其存在，这样，如果用户导航回实例，系统会使用描述Activity被销毁时状态的一组已保存数据创建Activity的新实例。 系统用于恢复先前状态的已保存数据被称为“实例状态”，并且是 Bundle 对象中存储的键值对集合。

<!--more-->

> 默认情况下，系统会使用 Bundle 实例状态保存您的Activity布局（比如，输入到 EditText 对象中的文本值）中有关每个 View 对象的信息。 这样，如果您的Activity实例被销毁并重新创建，布局状态便恢复为其先前的状态，且您无需代码。 但是，您的Activity可能具有您要恢复的更多状态信息，比如跟踪用户在Activity中进度的成员变量。

也就是说当系统在非用户自愿的情况下杀死Activity时，系统会调用这两个方法来保证不会给用户带来丢失数据的困扰

## 覆写这两个方法来实现设计者的高级需求

android默认的存储数据有时候可能不包含我们需要的信息，为了我们的需求，需要覆写对onSaveInstanceState()进行覆写

```
public void onRestoreInstanceState(Bundle savedInstanceState) {
    // Always call the superclass so it can restore the view hierarchy
    super.onRestoreInstanceState(savedInstanceState);

    // Restore state members from saved instance
    mCurrentScore = savedInstanceState.getInt(STATE_SCORE);
    mCurrentLevel = savedInstanceState.getInt(STATE_LEVEL);
}
```

然后在onCreate()里

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState); // Always call the superclass first

    // Check whether we're recreating a previously destroyed instance
    if (savedInstanceState != null) {
        // Restore value of members from saved state
        mCurrentScore = savedInstanceState.getInt(STATE_SCORE);
        mCurrentLevel = savedInstanceState.getInt(STATE_LEVEL);
    } 
}
```

当然也可以选择在onRestoreInstanceState()里进行覆写

```
public void onRestoreInstanceState(Bundle savedInstanceState) {
    // Always call the superclass so it can restore the view hierarchy
    super.onRestoreInstanceState(savedInstanceState);

    // Restore state members from saved instance
    mCurrentScore = savedInstanceState.getInt(STATE_SCORE);
    mCurrentLevel = savedInstanceState.getInt(STATE_LEVEL);
}
```

这样就不用判断saveInstanceState是否为空，因为只有在需要恢复数据的情况下系统才会调用这个方法
这个参数有时候还是很有用处的，比如你在做一款阅读APP，如果用户发现他把你的应用放进后台一会再打开的时候丢失了原来的页数，又变成了第一页，这样的体验一定是很差的，这时候就要用saveInstanceState来储存用户的页数