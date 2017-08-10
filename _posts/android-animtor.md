---
title: android基本动画
date: 2017-08-10 11:47:14
tags: Android
---

# android的四种基本动画

------

android里的动画可以分成四种基本类型

> - scale
> - alpha
> - rotate
> - translate

而动画既可以通过xml配置文件进行实现，也可以通过代码来操作。
通过xml的话较为方便，适合需要不断重用的动画，而代码操作起来则比较灵活方便，但是较为繁琐

## 通过xml文件

现在res文件夹下新建anim文件夹，然后在该目录下用xml文件配置好动画效果之后，只需要在代码中写下如下语句

```
Animation animation = AnimationUtils.loadAnimation(this,R.anim.scale);//载入xml文件
mImageView.startAnimation(animation);//使对应的View执行动画
```

### 缩放动画

```
<?xml version="1.0" encoding="utf-8"?>
<set
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:fillBefore="false"
    android:duration="1000">

    <scale
        android:fromXScale="0dp"
        android:fromYScale="0dp"
        android:toXScale="1.0"
        android:toYScale="1.0"
        android:pivotX="50%"
        android:pivotY="50%"/>
</set>
```

### 透明度动画

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <alpha
        android:duration="3000"
        android:fromAlpha="0.0"
        android:toAlpha="1.0"/>
</set>
```

### 旋转动画

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <rotate
        android:fromDegrees="0"
        android:pivotX="50%"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:pivotY="50%"
        android:duration="1000"
        android:toDegrees="+720"/>
</set>
```

### 位移动画

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate
        android:duration="3000"
        android:fromXDelta="-20"
        android:toXDelta="20"
        android:fromYDelta="-20"
        android:toYDelta="20"/>
</set>
```

## 通过代码控制

这一部分和之间区别不大，只不过是把xml文件里的内容放到代码里而已，下面我用位移动画来举例，其他则不再赘述

```
Animation animation = new TranslateAnimation(-50,50,0,0);
animation.setDuration(200);
animation.setRepeatCount(Animation.INFINITE);
animation.setRepeatMode(Animation.REVERSE);
mImageView.startAnimation(animation);
```

## 设置连续动画

单单是一种动画可能无法满足我们的实际需要，大部分情况下，我们需要把这些动画组合起来

### 通过设置动画的监听器来实现

```
animation = AnimationUtils.loadAnimation(this,R.anim.scale);
final Animation animation1 = AnimationUtils.loadAnimation(this,R.anim.rotate);
animation.setAnimationListener(new Animation.AnimationListener() {
    @Override
    public void onAnimationStart(Animation animation) {}
    @Override
    public void onAnimationEnd(Animation animation) {
        //当该动画结束之后自动执行下一个动画
        mImageView.startAnimation(animation1);
    }
    @Override
    public void onAnimationRepeat(Animation animation) {}
                });
mImageView.startAnimation(animation);
```

### 在xml文件里配置

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <alpha
        android:duration="3000"
        android:fromAlpha="0.0"
        android:toAlpha="1.0"/>
    <!--通过startOffset属性来设置动画推迟时间-->
    <rotate
        android:startOffset="3000"
        android:fromDegrees="0"
        android:pivotX="50%"
        android:pivotY="50%"
        android:duration="1000"
        android:toDegrees="+360"/>
</set>
```

之后像其他动画一样通过AnimationUtil载入就好

## 设置Activity的过渡动画

这个也很简单，直接上代码
zoom_in.xml

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
     android:interpolator="@android:anim/decelerate_interpolator"
     android:zAdjustment="top" >
    <alpha
        android:duration="10000"
        android:fromAlpha="0.2"
        android:toAlpha="1.0"/>

    <translate
        android:duration="10000"
        android:toXDelta="0%p"
        android:fromXDelta="100%p"/>
</set>
```

zoom_out.xml

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
     android:interpolator="@android:anim/decelerate_interpolator"
     android:zAdjustment="top" >
    <alpha
        android:duration="10000"
        android:fromAlpha="1.0"
        android:toAlpha="0.2"/>
</set>
```

在Activity中

```
Intent intent = new Intent(this,DemoActivity.class);
startActivity(intent);
//调用该方法覆盖系统的过度动画，需要传进activity退出和进入的配置
//必须放在startActivity方法后面
overridePendingTransition(R.anim.zoom_in,R.anim.zoom_out);
```

## 布局动画

布局动画是控制ViewGroup内的subView加载时的动画效果

```
Animation animation = AnimationUtils.loadAnimation(this,R.anim.rotate);
LayoutAnimationController controller = new LayoutAnimationController(animation);
controller.setOrder(LayoutAnimationController.ORDER_RANDOM);
mListView.setLayoutAnimation(controller);
```

## 帧动画

有空再写