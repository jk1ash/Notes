# autotools流程

## Autotools官方文档

[https://www.gnu.org/software/autoconf/manual/autoconf-2.63/html_node/index.html#Top](https://www.gnu.org/software/autoconf/manual/autoconf-2.63/html_node/index.html#Top)

## 一、准备：

1. 创建项目根目录
2. 创建src目录用来存放.c和.h 文件，并在 src 目录中新建 Makefile.am 文件
3. 创建data目录用来存
放.glade .desktop .sh 等数据文件，并在 data 目录中新建 Makefile.am 文件
4. 创建po目录用来存放国际化文件
5. 创建build-aux用来存放指定生成的辅助性脚本

## 二、步骤：

### (1) 使用autoscan命令自动生成configure.scan文件

### (2) 将configure.scan重命名为configure.ac, 并做适当修改

```
mv configure.scan configure.ac
```

### (3) 运行aclocal命令,生成"aclocal.m4"文件, 该文件主要处理本地的宏定义

### (4) 运行autoconf命令生成configure可执行文件

### (5) 运行autoheader命令, 生成config.h.in文件

### (6) 创建 Makefile.am

### (7) 运行automake命令, 生成Makefile.in文件

使用"--add-missing --copy"选项可以让automake自动添加一些必须的脚本文件

如果没有自动生成，则手动创建：

```
touch NEWS README AUTHORS ChangeLog
```

### (8)运行configure, 生成Makfefile文件和config.h文件

命令：

```
autoreconf -ivf  //可以重新生成configure
```

## 三、Makefile的使用:

### 编译源程序, 键入make, 默认执行"make all"命令

```
make
```

### 执行该命令,可以把程序安装到系统目录中

```
make install
```

### 删除install安装的文件

```
make uninstall
```

### 清除之前所编译的可执行文件及目标文件

```
make clean
```

### 将程序和相关的文档打包为一个压缩文档以供发布

```
make dist
```

### 删除由./configure 产生的文件。

```
make n distclean
```

## 使用autogen脚本进行自动编译

```
./autogen.sh //autogen.sh的作用是检测你的编译工具和依赖关系包是否完整
```

## 四、编写`configure.ac`

### 模版

```
#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.69])
AC_INIT([test], [1.0.0], [wang-jj@itep.com.cn])
AC_CONFIG_SRCDIR([src/test.c])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_AUX_DIR([build-aux])
AM_INIT_AUTOMAKE([-Wall -Werror])
AM_GNU_GETTEXT_VERSION([0.18.3])
AM_GNU_GETTEXT([external])

# Checks for programs.
AC_PROG_CC

# Checks for libraries.

# Checks for header files.

# Checks for typedefs, structures, and compiler characteristics.

# Checks for library functions.
PKG_CHECK_MODULES(GTK, [gtk+-3.0])
AC_SUBST(GTK_LIBS)
AC_SUBST(GTK_CFLAGS)

AC_CONFIG_FILES([Makefile po/Makefile.in src/Makefile])
AC_OUTPUT
```

### 说明

以"#"号开始的行为注释

---

AC_PREREQ宏声明本文要求的autoconf版本

---

```
AC_CONFIG_AUX_DIR([build-aux])
```

指定生成的辅助性脚本存放在build-aux目录下。可以不使用。

---

```
AC_INIT([FULL-PACKAGE-NAME], [VERSION], [BUG-REPORT-ADDRESS])
```

AC_INIT([名称], [版本], [bug提交邮箱])

---

```
AM_INIT_AUTOMAKE([-Wall -Werror])
```

检查 automake 尝试 Makefile 时的必要的工具。

---

```
AC_CONFIG_HEADERS([config.h])
```

检查头 HEADERS 并且给每一个发现的头

---

AC_CONFIG_SCRDIR宏用来侦测所指定的源码文件是否存在, 来确定源码目录的有效性.

---

```
AC_PROG_CC
```

编译器检测。

---

AC_CONFIG_FILES宏用于生成相应的Makefile文件

---

```
PKG_CHECK_MODULES(GTK, [gtk+-3.0 gmodule-export-2.0])
```

检测 gtk+-3.0 gmodule-export-2.0 模块

---

```
AC_SUBST(GTK_CFLAGS)
```

输出所有预处理器和编译器标志

---

```
AC_SUBST(GTK_LIBS)
```

输出所有链接器标志

### configure.ac启用debug

```
#Debug Options Support
AC_ARG_ENABLE([debug],
    [AS_HELP_STRING([--enable-debug],[debug program(default=no)])],
    [CFLAGS="${CFLAGS} -g -O0"],
    [CFLAGS="-g -O2"])
```

将上述Debug加入 configure.ac
编译时./configure加上--enbale-debug参数

或者不用debug宏，直接使用CFLAGS，CFLAGS后可接所有的gcc参数

也可在./configure时加入CFLAG=" "参数

```
CFLAGS=" "
```

gcc 警告参数
[https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html](https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html)

调试参数
[https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html#Debugging-Options](https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html#Debugging-Options)

## 五、编写`Makefile.am`

### 1.在根目录创建`Makefile.am`

```
SUBDIRS= po src

ACLOCAL_AMFLAGS = -I m4

EXTRA_DIST =  build-aux/config.rpath m4/ChangeLog
```

处理本目录之前要递归处理 src po子目录

### 2.在src创建`Makefile.am`

```
LIBS = -g $(GTK_LIBS)

AM_CPPFLAGS = -export-dynamic $(GTK_CFLAGS) -DLOCALEDIR=\"$(localedir)\"

bin_PROGRAMS = test

test_SOURCES = test.c
```

1.安装在$(bindir)中的可执行程序，程序名为test

2.列出test需要的源文件

3.为test附加编译选项

4.为test链接附加库

## 附录：常用安装目录变量

```
$(prefix) = /usr 或 /usr/local

$(bindir) = $(prefix)/bin

$(datadir) = $(prefix)/share

$(pkgdatadir)
#为deb包安装后的目录，为$(datadir)/xxx(pkgname)

$(includedir) = $(prefix)/include

$(libdir)
#32位下为 $(prefix)/lib/i386-linux-gnu <br/>
#64位下为 $(prefix)/lib/x86_64-linux-gnu
```
