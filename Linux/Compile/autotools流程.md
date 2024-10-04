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
## 六、国际化：
### 1.修改源码

#### a.添加头文件

```cpp
#include <glib/gi18n.h>
#include "config.h"
```

#### b.在main()函数中加入如下语句

```cpp
setlocale(LC_ALL,"");
bindtextdomain (PACKAGE, LOCALEDIR);
textdomain (PACKAGE);
```

#### c.使用_()宏将需要翻译的字符串包含起来:

```cpp
printf(_(“hello world!\n”));
```

### 2.修改configure.ac文件

```
AM_GNU_GETTEXT_VERSION([0.17])
AM_GNU_GETTEXT([external])
```

可先用`gettext -V`查看gettext的版本

a.使用的 gettext 版本

b.使用外部 gettext 库

c.添加这两句后执行

```
automake --add-missing --copy
```

会在build-aux 目录中生成 config.guess, config.sub 文件。

### 3. 修改 src/Makefile.am 链需要的库

```
AM_CPPFLAGS = -DLOCALEDIR=\"$(localedir)\"
```

### 4.执行 gettextize --copy

a. 这将会把gettextize需要的基础文件拷贝到当前目录,包括 po,

m4 目录， ABOUT-NLS, build-aux/config.rpath 文件。

b. gettextize 会修改 configure.ac

将：

```
AC_CONFIG_FILES([Makefile src/Makefile])
```

改为：

```
AC_CONFIG_FILES([Makefile src/Makefile po/Makefile.in])
```

c. gettextize 会修改 itep-monitorhotplug 目录下的

Makefile.am

将 ：

```
SUBDIRS = src
```

改为 ：

```
SUBDIRS = po src
ACLOCAL_AMFLAGE = -I m4
EXTRA_DIST = config.rpath
```

d. gettextize 会修改 ChangeLog 文件

### 5.把 po/Makevars.template 重命名 po/Makevars

### 6.往 po/POTFILES.in 中添加需要翻译的文件路径

```
src/demo.c
```

### 7. 执行 autoreconf -ivf

### 8. 执行./configure

### 9.在 po 目录下行 执行 make

在 po 目录下就会生成 xxx.pot。

### 10. 本地化翻译

在 po 目录下执行 msginit -l zh_CN 就会生成 zh_CN.po,编辑该文件。

```
"Content-Type: text/plain; charset=UTF-8\n"
#: src/demo.c:5
msgid "hello world"
msgstr "你好世界"
```

### 11.告诉gettext我们完成了zh_CN的翻译

在 po 目录下新建 LINGUAS 文件,并在文件中写入： zh_CN

### 12.返回上级目录，执行./configure

### 13.在po目录下执行 make update-po

生成 zh_CN.gmo

### 14.po和mo互相转换

```
# 先安装gettext
apt-get install gettext

# po->mo  
msgfmt  *.po -o *.mo

# mo->po
msgunfmt *.mo -o *.po
```
## 七、拆分库
### 1.src下(或其他目录,以src为例)添加xxx.pc.in文件

```
prefix=@prefix@
exec_prefix=@exec_prefix@
libdir=@libdir@
datarootdir=@datarootdir@
datadir=@datadir@
includedir=@includedir@/name #头文件的名称

#so名称需和libname这个一致
Name: libname 
Description:
Version: @VERSION@
Requires:
#name去掉lib
Libs: -L${libdir} -lname
Cflags: -I${includedir}
```

### 2.修改 configure.ac

添加用于编译pc.in

```
AC_CONFIG_FILES([src/xxx.pc])
```

### 3.修改 src/Makefile.am

添加用于编译pc.in文件

```
pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = xxx.pc
```

#### 4.debian下添加.install和dev.install文件

.install文件为 pkgname.install

dev.install文件为 pkgname-dev.install

如需要添加测试目录，则添加pkgname-test.install

```
#.install

usr/lib/*/*.so.*

#dev.install

usr/lib/*/*.so
usr/lib/*/pkgconfig/
usr/include/

#test.install
/usr/bin
```

### 5.修改debian/control

添加

```
Package: pkgname
Architecture: any
Depends: ${shlibs:Depends}, ${misc:Depends}
Description: <insert up to 60 chars description>
<insert long description, indented with spaces>

Package: pkgname-dev
Section: libdevel
Architecture: any
Depends: ${shlibs:Depends}, pkg-config, ${misc:Depends}
Description: <insert up to 60 chars description>
<insert long description, indented with spaces>

#需要添加测试包的话
Package: pkgname-test
Section: libdevel
Architecture: any
Depends: ${shlibs:Depends}, pkg-config, ${misc:Depends}
Description: <insert up to 60 chars description>
<insert long description, indented with spaces>
```

## 6.引用该库

### a.通过pc文件调用(推荐)

```
#先在configure.ac中添加，注意pcname需一致

PKG_CHECK_MODULES(LIBXXX, pcname)
AC_SUBST(LIBXXX_CFLAGS)
AC_SUBST(LIBXXX_LIBS)

#在Makefile.am中引用
XXX_CFLAGS = \
        $(LIBXXX_CFLAGS)
XXX_LDADD = \
        $(LIBXXX_LIBS)
```

### b.直接调用so库,solibname需和so库名称一致

```
#在Makefile.am中引用
installtool_LDADD = \
    -lsolibname
```


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
