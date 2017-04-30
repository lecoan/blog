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
import java.util.Map;

/**
 * Created by lecoan on 17-4-17.
 */
public abstract class AbstractCache<K, V> implements Cache<K, V> {
    //-1 不限制
    protected int cacheSize;
    protected long expire;
    protected Map<K, CacheObject<K, V>> cacheMap;

    public AbstractCache(int cacheSize, long expire) {
        this.cacheSize = cacheSize;
        this.expire = expire;
    }

    protected boolean exitItemExpire;

    @Override
    public void put(K key, V value) {
        CacheObject<K, V> cacheObject = new CacheObject<>(key, value, expire);
        if (isFull()) {
            eliminate();
        }
        cacheMap.put(key, cacheObject);
    }

    @Override
    public void put(K key, V value, long expire) {
        CacheObject<K, V> cacheObject = new CacheObject<>(key, value, expire);
        exitItemExpire = true;
        if (isFull()) {
            eliminate();
        }
        cacheMap.put(key, cacheObject);
    }

    @Override
    public V get(K key) {
        CacheObject<K, V> object = cacheMap.get(key);
        if (object == null) {
            return null;
        }
        if (object.isExpire()) {
            cacheMap.remove(key);
            return null;
        }
        return object.getValue();
    }

    @Override
    public int size() {
        return cacheMap.size();
    }

    protected boolean needEliminate() {
        return exitItemExpire;
    }

    protected abstract int eliminate();

    @Override
    public boolean isFull() {
        return cacheSize != -1 && cacheSize <= cacheMap.size();
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

    class CacheObject<K2, V2> {
        final K2 key;
        final V2 value;
        final long expire;
        long lastAccess;
        int accessTimes;

        CacheObject(K2 key, V2 value, long expire) {
            this.key = key;
            this.value = value;
            this.expire = expire;
        }

        boolean isExpire() {
            return expire != -1 && System.currentTimeMillis() - lastAccess >= expire;
        }

        K2 getKey() {
            return key;
        }

        V2 getValue() {
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

import java.util.Iterator;
import java.util.LinkedHashMap;
import java.util.Map;

/**
 * Created by lecoan on 17-4-14.
 */
public class LRUCache<K, V> extends AbstractCache<K, V> {


    public LRUCache(int cacheSize, long expire) {
        super(cacheSize, expire);
        /**
         * linkedHashMap已经实现了LRU算法，这里只需要让accessOrder为true，每次
         * 访问的 元素就会被提到双向链表的首部
         */
        cacheMap = new LinkedHashMap<K, CacheObject<K, V>>(cacheSize + 1, 1f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry eldest) {
                //当map的size为cacheSIze+1时，移除最后一个元素
                return LRUCache.this.removeEldestEntry();
            }
        };
    }

    private boolean removeEldestEntry() {
        return size() > cacheSize;
    }

    /**
     *这里不需要再对最后一个元素进行处理
     */
    @Override
    protected int eliminate() {
        if (!needEliminate())
            return 0;
        Iterator<CacheObject<K, V>> iterator = cacheMap.values().iterator();
        int count = 0;
        while (iterator.hasNext()) {
            CacheObject<K, V> object = iterator.next();
            if (object.isExpire()) {
                iterator.remove();
                count++;
            }
        }
        return count;
    }
}

```

## LFU
```java
import java.util.HashMap;
import java.util.Iterator;

/**
 * Created by lecoan on 4/30/17.
 */
public class LFUCache<K, V> extends AbstractCache<K, V> {

    public LFUCache(int cacheSize, long defaultExpire) {
        super(cacheSize, defaultExpire);
        cacheMap = new HashMap<>(cacheSize + 1);
    }

    @Override
    protected int eliminate() {
        if (!needEliminate())
            return 0;
        long minAccess = Long.MAX_VALUE;
        Iterator<CacheObject<K, V>> iterator = cacheMap.values().iterator();
        int count = 0;
        while (iterator.hasNext()) {
            CacheObject<K, V> object = iterator.next();
            if (object.isExpire()) {
                iterator.remove();
                count++;
            } else {
                //记录最少访问的次数
                minAccess = Math.min(minAccess, object.accessTimes);
            }
        }
        //如果缓存还是满的，移除访问最少的元素
        if (!isFull()) {
            Iterator<CacheObject<K, V>> iter = cacheMap.values().iterator();
            while (iterator.hasNext()) {
                CacheObject<K, V> object = iter.next();
                if (object.accessTimes <= minAccess) {
                    iterator.remove();
                    count++;
                }
            }
        }
        return count;
    }
}

```

## Random
```java
import java.util.HashMap;
import java.util.Iterator;
import java.util.Random;

/**
 * Created by lecoan on 4/30/17.
 */
public class RandomCache<K, V> extends AbstractCache<K, V> {

    public RandomCache(int cacheSize, long defaultExpire) {
        super(cacheSize, defaultExpire);
        cacheMap = new HashMap<>(cacheSize + 1);
    }

    @Override
    protected int eliminate() {
        if (!needEliminate())
            return 0;
        Iterator<CacheObject<K, V>> iterator = cacheMap.values().iterator();
        int count = 0, times = 0;
        //随机取一个小于当前map的数
        int rand = new Random().nextInt() % size();
        K key = null;
        while (iterator.hasNext()) {
            times++;
            CacheObject<K, V> object = iterator.next();
            if (object.isExpire()) {
                iterator.remove();
                count++;
            }
            //取得随机数对应的key值
            if (rand == times)
                key = object.getKey();
        }
        if (key != null && isFull()) {
            cacheMap.remove(key);
        }
        return count;
    }
}

```
## 最后
最气的还是本来觉得别人写的没有必要给删掉的代码，到最后还要乖乖加回去...