---
title: 设计模式系列(1) 单例模式
categories: 设计模式
---
# 介绍
所谓单例模式，就是让该对象在整个程序运行期间只存在不超过一个实例，所有对该类的调用都使用同一个实例
# 实现
实现很简单，关键步骤只有两步
* 构造函数私有化
* 提供类内部静态实例及静态获取方法

实现一般有两种，俗称饿汉模式和懒汉模式
<!--more-->
## 饿汉模式
```java
class Singleton {
    private Singleton(){/*do something*/}
    //程序启动时就初始化实例
    private static Singleton instance = new Singleton();
    //无需线程同步
    public static Singletion getInstance() {
        return instance;
    }
}
```
## 懒汉模式
```java
class Singleton {
    private Singleton(){/*do something*/}
    private static Singleton instance;
    //等到调用是才初始化实例
    public static Singletion getInstance() {
        //高并发下可能会出问题
        if(instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
## 比较
这两种实现选哪个呢？各有优劣。但我个人建议，选下者，因为饿汉模式在复杂逻辑下可能有坑，而且是天坑。
> 在实际开发环境中，一个单例内部可能使用到了其他类的单例，而类的加载是需要时间的。如果使用饿汉模式，有可能这个类创建时其持有的单例还没有加载好，进而导致程序已启动就崩掉

## PS:高并发下的懒汉模式
在高并发环境下，当两个以上的线程同时初次调用getInstance()时，可能会导致Singleton被创建两次，为此，我们需要进行线程同步
```java
public class Singleton { 

    private static volatile Singleton singleton;  

    private Singleton() {/*do something*/}  
    //双判断 
    public Singleton getInstance(){ 
        if(singleton == null){  
            synchronized (Singleton.class) {  
                //在线程锁未获得的时间里，可能有
                //别的类已经创建了实例
                if(singleton == null){ 
                    singleton = new Singleton();  
                }  
            }  
        }  
        return singleton;  
    }  
}  
```
# 参考
* [高并发下的单例](http://blog.csdn.net/cselmu9/article/details/51366946)
* [解决高并发下的单例模式](http://www.debugrun.com/a/31948py.html)
* [volatile关键字解析](http://www.cnblogs.com/dolphin0520/p/3920373.html)
