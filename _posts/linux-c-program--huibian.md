---
title: 一站式C编程-汇编
tags:
- Linux
- C
categories: Linux
---

# 一站式C编程-汇编

##认识汇编

​	无论使用什么语言编程，最终都是要转化成机器码之后计算机才能执行。对早期的程序员来说，当时并没有现在 的这些高级语言，而使用机器码效率又慢又不容易查错，因此人们发明了汇编语法来进行助记，并使用汇编器来将汇编代码转换为机器码。

<!--more-->

### 编译汇编代码

先看一段简单的汇编代码

```assembly
#PURPOSE: 退出并向Linux内核返回一个状态码
#VARIABLES:
#	  %eax 保存系统调用号
#	  %ebx 保存返回状态
 .section .data

 .section .text
 .globl _start
_start:
 movl $1, %eax	# 1是Linux退出程序操作的调用号
 movl $4, %ebx	# 4为返回状态码，可改

 int $0x80	#发生软中断退出程序
```

将该文件保存为`simple.S`文件，首先用汇编器`as`(assembler)把汇编代码里的助记符翻译为机器指令

```shell
as simple.S -o simple.o 
```

然后使用链接器`ld`进行链接，生成可执行文件

```sh
ld simple.o -o simple
```



在Linux中通过`$?`可以看到上一个程序的返回状态，执行

```shell
echo $?
```

可以看到返回值为`4`，正是汇编中`%ebx`中保存的值

### 语法分析

先看第一句，汇编程序中以`.`开头的名称并不是指令的助记符，不会被翻译成机器指令，而是给汇编器一些特殊指示，称为汇编指示或伪操作。`.section`指示把代码划分成若干个段（Section），程序被操作系统加载执行时，每个段被加载到不同的地址，操作系统对不同的页面设置不同的读、写、执行权限。`.data`段保存程序的数据，是可读可写的，相当于C程序的全局变量。本程序中没有定义数据，所以`.data`段是空的。

```assembly
.section .data
```

`.text`段保存代码，是只读和可执行的，后面那些指令都属于`.text`段。

```assembly
 .section .text
```

`.globl`表明

```assembly
 .globl _start
```

在汇编程序中，立即数前面要加$，寄存器名前面要加% 。`movl`指令还有另外几种形式，但数据传送方向都是一样的，第一个操作数总是源操作数，第二个操作数总是目标操作数。

```assembly
movl $1, %eax
movl $4, %ebx
```

`eax`和`ebx`的值是传递给系统调用的两个参数。`eax`的值是系统调用号，Linux的各种系统调用都是由`int $0x80`指令引发的，内核需要通过`eax`判断用户要调哪个系统调用，`_exit`的系统调用号是1。`ebx`的值是传给`_exit`的参数，表示退出状态。大多数系统调用完成之后会返回用户空间继续执行后面的指令，而`_exit`系统调用比较特殊，它会终止掉当前进程，而不是返回用户空间继续执行。

```assembly
int $0x80
```



x86的通用寄存器有`eax`、`ebx`、`ecx`、`edx`、`edi`、`esi`。除法指令`idivl`要求被除数在`eax`寄存器中，`edx`寄存器必须是0，而除数可以在任意寄存器中，计算结果的商数保存在`eax`寄存器中（覆盖原来的被除数），余数保存在`edx`寄存器中。也就是说，通用寄存器对于某些特殊指令来说也不是通用的。x86的特殊寄存器有`ebp`、`esp`、`eip`、`eflags`。`eip`是程序计数器，`eflags`保存着计算过程中产生的标志位

- `.byte`，也是声明一组数，每个数占8位
- `.long`指示声明一组数，每个数占32位，相当于C语言中的数组
- `.ascii`，例如`.ascii "Hello world"`，声明11个数，取值为相应字符的ASCII码。注意，和C语言不同，这样声明的字符串末尾是没有`'\0'`字符的，如果需要以`'\0'`结尾可以声明为`.ascii "Hello world\0"`。
- ​

1. 写一个汇编程序保存成文本文件`max.s`。
2. 汇编器读取这个文本文件转换成目标文件`max.o`，目标文件由若干个Section组成，我们在汇编程序中声明的`.section`会成为目标文件中的Section，此外汇编器还会自动添加一些Section（比如符号表）。
3. 然后链接器把目标文件中的Section合并成几个Segment[[28](http://docs.huihoo.com/c/linux-c-programming/ch18s05.html#ftn.id2770769)]，生成可执行文件`max`。
4. 最后加载器（Loader）根据可执行文件中的Segment信息加载运行这个程序。



## 汇编与C的关系

## 可执行文件格式-ELF文件

ELF文件格式是一个开放标准，各种UNIX系统的可执行文件都采用ELF格式，它有三种不同的类型：

- 可重定位的目标文件（Relocatable，或者Object File）
- 可执行文件（Executable）
- 共享库（Shared Object，或者Shared Library）

ELF格式提供了两种不同的视角，链接器把ELF文件看成是Section的集合，而加载器把ELF文件看成是Segment的集合。如下图所示。

**图 18.1. ELF文件**

![ELF文件](http://docs.huihoo.com/c/linux-c-programming/images/asm.elfoverview.png)

使用`readelf`工具进行读取

