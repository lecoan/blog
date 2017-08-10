---
title: fragment生命周期
date: 2017-08-10 11:40:59
tags: Android
---

# fragment的生命周期

fragment是android3.0之后新引入的组件，能够嵌在Activity中使用，可以看成是轻型的Activity，因此Fragment也有他自己的生命周期。
[![frgment生命周期](http://img.blog.csdn.net/20130513093815793)](http://img.blog.csdn.net/20130513093815793)frgment生命周期

> - 创建部分
>   1. **onAttach()**
>      当fragment第一次被添加进Activity时调用，将fragment和Activity进行关联
>   2. **onCreate()**
>      当fragment第一次被创建的时候调用，发生在Activity的onAttachFragment()之后，在这里你可以对任何非View层的数据进行初始化，比如getIntent()之类的，但绝对不要动View层的东西，因为这个方法可以在Activity的onCreate方法还没执行完的时候调用
>   3. **onCreateView()**
>      inflate对应fragment的View，在这里你可以对View的元素进行初始化，比如findViewById()，但注意不要使用getView()来调用findViewById()因为getView()只有在该方法结束的时候才能获取到对象
>   4. **onActivityCreated()**
>      当Activity的onCreate()方法调用完毕后被执行，在这里你可以做所有的修改，比如更改View的显示
> - 前台显示
>   1. **onStart()**
>      当这个fragment对用户可见时调用。通常它依赖于包含它的activity的Activity.onStart
>   2. **onResume()**
>      当这个fragment对用户可见并且正在运行时调用。通常它依赖于包含它的activity的Activity.onResume
> - 后台隐藏
>   1. **onPause()**
>      当这个fragment对用户部分不可见时调用。通常它依赖于包含它的activity的Activity.onPause
>   2. **onStop()**
>      当这个fragment对用户完全不可见时调用。通常它依赖于包含它的activity的Activity.onStop
> - 销毁状态
>   1. **onDestroyView()**
>      将View与fragment剥离时被调用
>   2. **onDestroy()**
>   3. **onDetach()**