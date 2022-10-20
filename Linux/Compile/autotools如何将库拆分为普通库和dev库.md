# 如何将库拆分为普通库和dev库

## 1.src下(或其他目录,以src为例)添加xxx.pc.in文件

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

## 2.修改 configure.ac

添加用于编译pc.in

```
AC_CONFIG_FILES([src/xxx.pc])
```

## 3.修改 src/Makefile.am

添加用于编译pc.in文件

```
pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = xxx.pc
```

## 4.debian下添加.install和dev.install文件

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

## 5.修改debian/control

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
