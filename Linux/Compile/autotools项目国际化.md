# 项目国际化

## 1.修改源码

### a.添加头文件

```cpp
#include <glib/gi18n.h>
#include "config.h"
```

### b.在main()函数中加入如下语句

```cpp
setlocale(LC_ALL,"");
bindtextdomain (PACKAGE, LOCALEDIR);
textdomain (PACKAGE);
```

### c.使用_()宏将需要翻译的字符串包含起来:

```cpp
printf(_(“hello world!\n”));
```

## 2.修改configure.ac文件

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

## 3. 修改 src/Makefile.am 链需要的库

```
AM_CPPFLAGS = -DLOCALEDIR=\"$(localedir)\"
```

## 4.执行 gettextize --copy

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

## 5.把 po/Makevars.template 重命名 po/Makevars

## 6.往 po/POTFILES.in 中添加需要翻译的文件路径

```
src/demo.c
```

## 7. 执行 autoreconf -ivf

## 8. 执行./configure

## 9.在 po 目录下行 执行 make

在 po 目录下就会生成 xxx.pot。

## 10. 本地化翻译

在 po 目录下执行 msginit -l zh_CN 就会生成 zh_CN.po,编辑该文件。

```
"Content-Type: text/plain; charset=UTF-8\n"
#: src/demo.c:5
msgid "hello world"
msgstr "你好世界"
```

## 11.告诉gettext我们完成了zh_CN的翻译

在 po 目录下新建 LINGUAS 文件,并在文件中写入： zh_CN

## 12.返回上级目录，执行./configure

## 13.在po目录下执行 make update-po

生成 zh_CN.gmo

---

## 错误：ERROR "'LC_ALL' undeclared"

加入头文件

```
#include <locale.h>
```

## po和mo互相转换

```
# 先安装gettext
apt-get install gettext

# po->mo  
msgfmt  *.po -o *.mo

# mo->po
msgunfmt *.mo -o *.po
```
