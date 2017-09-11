---
title: 一站式C编程-终端
tags:
- Linux
- C 
categories: Linux
---

# 一站式C编程 - 终端

### 1.2. 终端登录过程

一台PC通常只有一套键盘和显示器，也就是只有一套终端设备，但是可以通过Ctrl-Alt-F1~Ctrl-Alt-F6切换到6个字符终端，相当于有6套虚拟的终端设备，它们共用同一套物理终端设备，对应的设备文件分别是`/dev/tty1`~`/dev/tty6`，所以称为虚拟终端（Virtual Terminal）。<!--more-->设备文件`/dev/tty0`表示当前虚拟终端，比如切换到Ctrl-Alt-F1的字符终端时`/dev/tty0`就表示`/dev/tty1`，切换到Ctrl-Alt-F2的字符终端时`/dev/tty0`就表示`/dev/tty2`，就像`/dev/tty`一样也是一个通用的接口，但它不能表示图形终端窗口所对应的终端。

**例 34.1. 查看终端对应的设备文件名**

```c
#include <unistd.h>
#include <stdio.h>

int main()
{
    printf("fd 0: %s\n", ttyname(0));
    printf("fd 1: %s\n", ttyname(1));
    printf("fd 2: %s\n", ttyname(2));
    return 0;
}
```

做嵌入式开发时经常会用到串口终端，目标板的每个串口对应一个终端设备，比如`/dev/ttyS0`、`/dev/ttyS1`等，将主机和目标板用串口线连起来，就可以在主机上通过Linux的`minicom`或Windows的超级终端工具登录到目标板的系统。

## 作业控制

