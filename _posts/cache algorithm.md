---
title: 基本缓存算法 - java实现
tags: java
categories: algorithm
---

# 概述
这个学期的程序设计大作业用到了缓存的概念，刚好这周计算机组成原理也讲到了几种常见的缓存算法，正好在这个地方记录一下，以防遗忘

## 什么是缓存？
通俗来说，缓存是“存贮数据（使用频繁的数据）的临时地方，因为取原始数据的代价太大了，所以可以取得快一些。我们都听说过线程池、对象池，缓存可以认为是数据的池，这些数据是从数据库里的真实数据复制出来的，并且为了能别取回，被标上了标签（键 ID）。
## 缓存算法分类
* FIFO 先来的先被移走
* LFU 使用最不频繁的被移走
* LRU 最近使用最不频繁的被移走
* Random Cache 随机选一个移走

**注意Random Cache实际上的性能仅仅稍差于LFU和LRU**
<!--more-->
# 接口定义

```java
package cache;
/**
 * Created by lecoan on 17-4-17.
 */
public interface Cache<K, V> {

    void put(K key, V value);

    void put(K key, V value, long expire);

    V get(K key);

    int size();

    int maxSize();

    boolean isFull();

    boolean isEmpty();

    void clear();
}
```

# 实现
**这份代码的实现参考了jodd.cache包里的代码**<br>~~几乎和人家写的一模一样也好意思说参考？~~
## 抽象基础类实现

```java
package cache;

import java.util.Map;

/**
 * Created by lecoan on 17-4-17.
 */
public abstract class AbstractCache<K, V> implements Cache<K,V> {

    //-1 不限制
    protected int cacheSize;
    protected long expire;

    protected Map<K,CacheObject<V>> cacheMap;

    public AbstractCache(int cacheSize, long expire){
        this.cacheSize = cacheSize;
        this.expire = expire;
    }

    protected boolean exitItemExpire;

    @Override
    public void put(K key, V value) {
        CacheObject<V> cacheObject = new CacheObject<>(value,expire);
        if(isFull()){
            eliminate();
        }
        cacheMap.put(key,cacheObject);
    }

    @Override
    public void put(K key, V value, long expire) {
        CacheObject<V> cacheObject = new CacheObject<>(value,expire);
        exitItemExpire = true;
        if(isFull()){
            eliminate();
        }
        cacheMap.put(key,cacheObject);
    }

    @Override
    public V get(K key) {
        CacheObject<V> object = cacheMap.get(key);
        if(object==null){
            return null;
        }
        if(object.isExpire()){
            cacheMap.remove(key);
            return null;
        }
        return object.getValue();
    }

    @Override
    public int size() {
        return cacheMap.size();
    }
    
    protected boolean needEliminate(){
        return exitItemExpire;
    }
    
    protected abstract int eliminate();

    @Override
    public boolean isFull() {
        return cacheSize != -1&&cacheSize<=cacheMap.size();
    }

    @Override
    public int maxSize() {
        return cacheSize;
    }

    @Override
    public boolean isEmpty() {
        return cacheMap.isEmpty();
    }

    @Override
    public void clear() {
        cacheMap.clear();
    }

    class CacheObject<V2> {
        final V2 value;
        final long expire;
        long lastAccess;
        int accessTimes;
        CacheObject(V2 value, long expire) {
            this.value = value;
            this.expire = expire;
        }
        boolean isExpire() {
            return expire != -1 && System.currentTimeMillis() - lastAccess >= expire;
        }
        V2 getValue(){
            accessTimes++;
            lastAccess = System.currentTimeMillis();
            return value;
        }
    }
}
```

## FIFO
```java

```

## LRU
```java

```

## LFU
```java

```

## Random
```java

```