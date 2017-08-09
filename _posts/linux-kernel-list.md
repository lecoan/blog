---
title: Linux Kernel中的双向链表
tags: Linux 数据结构
categories: Linux
---

# Linux Kernel中的双向链表

在Linux内核源码中双向链表的到了大量的使用，Linux在/include/nvif/list.h中定义了其数据结构

```C
struct list_head {
    struct list_head *next, *prev;
};
```

<!--readmore-->

## 使用方式

先定义自己的数据结构，并将list_head嵌入

```C
struct my_list {
  	// ...
	int * data; //data可以是任何数据类型
  	// ...
  	list_head head;
}
```

利用结构体中的list_head可以将my_list连接起来

Linux提供了list_entry来获取链表中对应的数据

```C
list_entry(ptr, type, member)
```

* ptr --> list_head的指针
* type --> 自定义的结构，例子中应是struct my_list
* member --> 需要获取的数据名，例子中应是data

## 实现原理

list_entry定义在include/linux/list.h中

```C
#define list_entry(ptr, type, member) \
	container_of(ptr, type, member)
```

可以看到list_entry实际上就是container_of

container_of定义如下

```C
#define container_of(ptr, type, member) \
    (type *)((char *)(ptr) - (char *) &((type *)0)->member)
```

这里用来一个非常巧妙的转换，将ptr的值减去member相对于0H的偏移量，就得到了实际内存中member的地址，最后进行一次指针类型的强转