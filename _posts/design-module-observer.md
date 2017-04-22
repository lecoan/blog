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
    >当被观测者发生改变时，将所有信息都发送给所有观测者
* pull模式
    >当被观测者发生改变时，只通知对象发生改变，具体的内容需要观测者主动获取

在Java中提供了基本的接口可供继承实现
## Observer
```java
public interface Observer {

    //可以看到java.util中的观测者是采用了pull模式来实现的
    //观测者可以根据arg参数来确定对Observable
    //对象进行特定数据的获取和操作
    void update(Observable o, Object arg);
}
```
## Observable
Observable类里的绝大多数方法都非常简单，这里我们把关注重点放在notifyObservers()上
```java
public class Observable {

    private boolean changed = false;
    private Vector<Observer> obs;

    public Observable() {
        obs = new Vector<>();
    }

    public synchronized void addObserver(Observer o) {
        if (o == null)
            throw new NullPointerException();
        if (!obs.contains(o)) {
            obs.addElement(o);
        }
    }

    public synchronized void deleteObserver(Observer o) {
        obs.removeElement(o);
    }

    public void notifyObservers() {
        notifyObservers(null);
    }

    /*
     * 这个地方有可能出现两种比较糟糕的情况
     * 1. 在执行过程中已经取消了观测的对象可能依然会被通知
     * 2. 新注册的用户会收不到通知
     */
    public void notifyObservers(Object arg) {
         //一个用于储存当前所有Observers的瞬间状态的辅助数组
        Object[] arrLocal;

        synchronized (this) {
            if (!changed)
                return;
            //toArray仅仅是将对象引用进行了拷贝
            arrLocal = obs.toArray();
            clearChanged();
        }
        //不直接用Vector的原因是防止在遍历的过程中有新的Observer被注册
        for (int i = arrLocal.length-1; i>=0; i--)
            ((Observer)arrLocal[i]).update(this, arg);

    public synchronized void deleteObservers() {
        obs.removeAllElements();
    }

    protected synchronized void setChanged() {
        changed = true;
    }

    protected synchronized void clearChanged() {
        changed = false;
    }

    public synchronized boolean hasChanged() {
        return changed;
    }

    public synchronized int countObservers() {
        return obs.size();
    }
}
```
