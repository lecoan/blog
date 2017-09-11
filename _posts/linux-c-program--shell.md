---

title: 一站式C编程-shell
tags:
- Linux
- C
categories: Linux

---

# 一站式C编程--shell脚本编写



Shell的作用是解释执行用户的命令，用户输入一条命令，Shell就解释执行一条，这种方式称为交互式（Interactive），Shell还有一种执行命令的方式称为批处理（Batch），<!---more-->用户事先写一个Shell脚本（Script），其中有很多条命令，让Shell一次把这些命令执行完，而不必一条一条地敲命令。Shell脚本和编程语言很相似，也有变量和流程控制语句，但Shell脚本是解释执行的，不需要编译，Shell程序从脚本中一行一行读取并执行这些命令，相当于一个用户把脚本中的命令一行一行敲到Shell提示符下执行。

文件`/etc/shells`给出了系统中所有已知（不一定已安装）的Shell，除了上面提到的Shell之外还有很多变种。

```shell
# /etc/shells: valid login shells
/bin/csh
/bin/sh
/usr/bin/es
/usr/bin/ksh
/bin/ksh
/usr/bin/rc
/usr/bin/tcsh
/bin/tcsh
/usr/bin/esh
/bin/dash
/bin/bash
/bin/rbash
/usr/bin/screen
```

用户的默认Shell设置在`/etc/passwd`文件中，例如下面这行对用户lecoan的设置:

```shell
lecoan:x:1000:1000::/home/lecoan:/bin/zsh
```

用户在命令行输入命令后，一般情况下Shell会`fork`并`exec`该命令(fork??)，但是Shell的内建命令例外，执行内建命令相当于调用Shell进程中的一个函数，并不创建新的进程!!!(pwd)。以前学过的`cd`、`alias`、`umask`、`exit`等命令即是内建命令，凡是用`which`命令查不到程序文件所在位置的命令都是内建命令，内建命令没有单独的man手册，要在man手册中查看内建命令，应该

```shell
$ man bash-builtins
```

本节会介绍很多内建命令，如`export`、`shift`、`if`、`eval`、`[`、`for`、`while`等等

Shell脚本中用`#`表示注释，相当于C语言的`//`注释。但如果`#`位于第一行开头，并且是`#!`（称为Shebang）则例外，它表示该脚本使用后面指定的解释器`/bin/sh`解释执行

`exec`还有另外一种机制，如果要执行的是一个文本文件，并且第一行用Shebang指定了解释器，则用解释器程序的代码段替换当前进程，并且从解释器的`_start`开始执行



```shell
#! /bin/sed -f
```

执行`./script`相当于执行程序

```shell
$ /bin/sed -f ./script.sh
```

1. 交互Shell（`bash`）`fork`/`exec`一个子Shell（`sh`）用于执行脚本，父进程`bash`等待子进程`sh`终止。

如果将命令行下输入的命令用()括号括起来，那么也会`fork`出一个子Shell执行小括号中的命令，一行中可以输入由分号;隔开的多个命令，比如：

```
$ (cd ..;ls -l)
```

和上面两种方法执行Shell脚本的效果是相同的，`cd ..`命令改变的是子Shell的`PWD`，而不会影响到交互式Shell。然而命令

```
$ cd ..;ls -l
```

则有不同的效果，`cd ..`命令是直接在交互式Shell下执行的，改变交互式Shell的`PWD`，然而这种方式相当于这样执行Shell脚本：

```
$ source ./script.sh
```

或者

```
$ . ./script.sh
```

`source`或者`.`命令是Shell的内建命令，这种方式也不会创建子Shell，而是直接在交互式Shell下逐行执行脚本中的命令。

## 变量

变量都以字符串形式保存

按照惯例，Shell变量由全大写字母加下划线组成，有两种类型的Shell变量：

* 环境变量

  环境变量可以从父进程传给子进程，因此Shell进程的环境变量可以从当前Shell进程传给fork出来的子进程。用printenv命令可以显示当前Shell进程的环境变量

  ```shell
  export VARNAME=value
  ```

* 本地变量

* ```shell
  VARNAME=value
  ```

用`unset`命令可以删除已定义的环境变量或本地变量。

```shell
unset VARNAME
```

如果一个变量叫做`VARNAME`，用`${VARNAME}`可以表示它的值，在不引起歧义的情况下也可以用`$VARNAME`表示它的值

### 文件名代换（Globbing）：* ? []

| *      | 匹配0个或多个任意字符       |
| ------ | ----------------- |
| ?      | 匹配一个任意字符          |
| [若干字符] | 匹配方括号中任意一个字符的一次出现 |

### 命令代换：`或 $()

由反引号括起来的也是一条命令，Shell先执行该命令，然后将输出结果立刻代换到当前命令行中。例如定义一个变量存放`date`命令的输出：

```shell
DATE=`date`
echo $DATE
```

### 算术代换：$(())

用于算术计算，`$(())`中的Shell变量取值将转换成整数，例如：

```
$ VAR=45
$ echo $(($VAR+3))
```

### 转义字符\

还有一个字符虽然不具有特殊含义，但是要用它做文件名也很麻烦，就是-号。如果要创建一个文件名以-号开头的文件

```shell
touch ./-hello
```

或者

```shell
touch -- -hello
```

```

```

\还有一种用法，在\后敲回车表示续行，Shell并不会立刻执行命令，而是把光标移到下一行，给出一个续行提示符>，等待用户继续输入，最后把所有的续行接到一起当作一个命令

### 单引号

和C语言不一样，Shell脚本中的单引号和双引号一样都是字符串的界定符（双引号下一节介绍），而不是字符的界定符。单引号用于保持引号内所有字符的字面值，即使引号内的\和回车也不例外，但是字符串中不能出现单引号

### 双引号

双引号用于保持引号内所有字符的字面值（回车也不例外），但以下情况除外：

- $加变量名可以取变量的值
- 反引号仍表示命令替换
- \$表示$的字面值
- \`表示`的字面值
- \"表示"的字面值
- \\表示\的字面值
- 除以上情况之外，在其它字符前面的\无特殊含义，只表示字面值

## 启动脚本

启动脚本是`bash`启动时自动执行的脚本。用户可以把一些环境变量的设置和`alias`、`umask`设置放在启动脚本中，这样每次启动Shell时这些设置都自动生效（source执行）

启动bash的方法不同，执行启动脚本的步骤也不相同

* 作为交互登录Shell启动，或者使用--login参数启动

  1. 首先执行`/etc/profile`，系统中每个用户登录时都要执行这个脚本，如果系统管理员希望某个设置对所有用户都生效，可以写在这个脚本里
  2. 然后依次查找当前用户主目录的`~/.bash_profile`、`~/.bash_login`和`~/.profile`三个文件，找到第一个存在并且可读的文件来执行，如果希望某个设置只对当前用户生效，可以写在这个脚本里，由于这个脚本在`/etc/profile`之后执行，`/etc/profile`设置的一些环境变量的值在这个脚本中可以修改，也就是说，当前用户的设置可以覆盖（Override）系统中全局的设置。`~/.profile`这个启动脚本是`sh`规定的，`bash`规定首先查找以`~/.bash_`开头的启动脚本，如果没有则执行`~/.profile`，是为了和`sh`保持一致。
  3. 顺便一提，在退出登录时会执行`~/.bash_logout`脚本（如果它存在的话）。

* 非交互式登陆

  Shell在启动时自动执行`~/.bashrc`脚本

为什么登录Shell和非登录Shell的启动脚本要区分开呢？最初的设计是这样考虑的，如果从字符终端或者远程登录，那么登录Shell是该用户的所有其它进程的父进程，也是其它子Shell的父进程，所以环境变量在登录Shell的启动脚本里设置一次就可以自动带到其它非登录Shell里，而Shell的本地变量、函数、`alias`等设置没有办法带到子Shell里，需要每次启动非登录Shell时设置一遍，所以就需要有非登录Shell的启动脚本，所以一般来说在`~/.bash_profile`里设置环境变量，在`~/.bashrc`里设置本地变量、函数、`alias`等。如果你的Linux带有图形系统则不能这样设置，由于从图形界面的窗口管理器登录并不会产生登录Shell，所以环境变量也应该在`~/.bashrc`里设置

* 非交互启动

  为执行脚本而`fork`出来的子Shell是非交互Shell，启动时执行的脚本文件由环境变量`BASH_ENV`定义，相当于自动执行以下命令：

  ```
  if [ -n "$BASH_ENV" ]; then . "$BASH_ENV"; fi
  ```

  如果环境变量`BASH_ENV`的值不是空字符串，则把它的值当作启动脚本的文件名，`source`这个脚本

## 语法

### 条件测试：test 或 [ ]

命令`test`或`[`可以测试一个条件是否成立，如果测试结果为真，则该命令的Exit Status为0，如果测试结果为假，则命令的Exit Status为1

| `[ -d DIR ]`             | 如果`DIR`存在并且是一个目录则为真                      |
| ------------------------ | ---------------------------------------- |
| `[ -f FILE ]`            | 如果`FILE`存在且是一个普通文件则为真                    |
| `[ -z STRING ]`          | 如果`STRING`的长度为零则为真                       |
| `[ -n STRING ]`          | 如果`STRING`的长度非零则为真                       |
| `[ STRING1 = STRING2 ]`  | 如果两个字符串相同则为真                             |
| `[ STRING1 != STRING2 ]` | 如果字符串不相同则为真                              |
| `[ ARG1 OP ARG2 ]`       | `ARG1`和`ARG2`应该是整数或者取值为整数的变量，`OP`是`-eq`（等于）`-ne`（不等于）`-lt`（小于）`-le`（小于等于）`-gt`（大于）`-ge`（大于等于）之中的一个 |

| `[ ! EXPR ]`         | `EXPR`可以是上表中的任意一种测试条件，!表示逻辑反             |
| -------------------- | ---------------------------------------- |
| `[ EXPR1 -a EXPR2 ]` | `EXPR1`和`EXPR2`可以是上表中的任意一种测试条件，`-a`表示逻辑与 |
| `[ EXPR1 -o EXPR2 ]` | `EXPR1`和`EXPR2`可以是上表中的任意一种测试条件，`-o`表示逻辑或 |

作为一种好的Shell编程习惯，应该总是把变量取值放在双引号之中

`read`命令的作用是等待用户输入一行字符串，将该字符串存到一个Shell变量中

`:`是一个特殊的命令，称为空命令，该命令不做任何事，但Exit Status总是真

```shell
#! /bin/sh

echo "Is it morning? Please answer yes or no."
read YES_OR_NO
if [ "$YES_OR_NO" = "yes" ]; then
  echo "Good morning!"
elif [ "$YES_OR_NO" = "no" ]; then
  echo "Good afternoon!"
else
  echo "Sorry, $YES_OR_NO not recognized. Enter yes or no."
  exit 1
fi
exit 0
```

Shell还提供了&&和||语法，和C语言类似，具有Short-circuit特性

### case/esac

，`esac`表示`case`语句块的结束。C语言的`case`只能匹配整型或字符型常量表达式，而Shell脚本的`case`可以匹配字符串和Wildcard，每个匹配分支可以有若干条命令，末尾必须以;;结束，执行时找到第一个匹配的分支并执行相应的命令，然后直接跳到`esac`之后，不需要像C语言一样用`break`跳出。

```shell
#! /bin/sh

echo "Is it morning? Please answer yes or no."
read YES_OR_NO
case "$YES_OR_NO" in
yes|y|Yes|YES)
  echo "Good Morning!";;
[nN]*)
  echo "Good Afternoon!";;
*)
  echo "Sorry, $YES_OR_NO not recognized. Enter yes or no."
  exit 1;;
esac
exit 0
```

`$1`是一个特殊变量，在执行脚本时自动取值为第一个命令行参数

Shell脚本的`for`循环结构和C语言很不一样，它类似于某些编程语言的`foreach`循环。例如：

```shell
#! /bin/sh

for FRUIT in apple banana pear; do
  echo "I like $FRUIT"
done
```

```shell
for FILENAME in `ls chap?`; do mv $FILENAME $FILENAME~; done
```

`while`的用法和C语言类似。比如一个验证密码的脚本：

```shell
#! /bin/sh

echo "Enter password:"
read TRY
while [ "$TRY" != "secret" ]; do
  echo "Sorry, try again"
  read TRY
done
```

**常用的位置参数和特殊变量**

| `$0`         | 相当于C语言`main`函数的`argv[0]`                 |
| ------------ | ---------------------------------------- |
| `$1`、`$2`... | 这些称为位置参数（Positional Parameter），相当于C语言`main`函数的`argv[1]`、`argv[2]`... |
| `$#`         | 相当于C语言`main`函数的`argc - 1`，注意这里的`#`后面不表示注释 |
| `$@`         | 表示参数列表`"$1" "$2" ...`，例如可以用在`for`循环中的`in`后面。 |
| `$?`         | 上一条命令的Exit Status                        |
| `$$`         | 当前Shell的进程号                              |

位置参数可以用`shift`命令左移。比如`shift 3`表示原来的`$4`现在变成`$1`，原来的`$5`现在变成`$2`等等，原来的`$1`、`$2`、`$3`丢弃，`$0`不移动

和C语言类似，Shell中也有函数的概念，但是函数定义中没有返回值也没有参数列表。例如：

```shell
#! /bin/sh

foo(){ echo "Function foo is called";}
echo "-=start=-"
foo
echo "-=end=-"
```

但可以传参

## Shell脚本的调试方法

Shell提供了一些用于调试脚本的选项，如下所示：

- -n

  读一遍脚本中的命令但不执行，用于检查脚本中的语法错误

- -v

  一边执行脚本，一边将执行过的脚本命令打印到标准错误输出

- -x

  提供跟踪执行信息，将执行的每一条命令和结果依次打印出来

使用这些选项有三种方法，一是在命令行提供参数

```
$ sh -x ./script.sh
```

二是在脚本开头提供参数

```
#! /bin/sh -x
```

第三种方法是在脚本中用set命令启用或禁用参数

```
#! /bin/sh
if [ -z "$1" ]; then
  set -x
  echo "ERROR: Insufficient Args."
  exit 1
  set +x
fi
```
