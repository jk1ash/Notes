## meson

```bash
meson build && cd build

ninja
```

## cmake

```bash
mkdir build

cd build
cmake ..
make
```

## pavucontrol-qt更新中文翻译

```
# 使用lxqt-transupdate 更新翻译

# 更新翻译
lupdate . -ts src/translations/pavucontrol-qt/pavucontrol-qt_zh_CN.ts -locations absolute
# 更新qm文件
lrelease src/translations/pavucontrol-qt/pavucontrol-qt_zh_CN.ts
```

## gtk编译

```
#2.0
gcc main.c -o main `pkg-config --libs --cflags gtk+-2.0`

#3.0
gcc main.c -o main `pkg-config --libs --cflags gtk+-3.0`

#glib
gcc mian.c -o main `pkg-config --libs --cflags glib-2.0`
```

## 使用dbus-binding-tool从.xml生成.h

```
dbus-binding-tool --mode=glib-server --prefix=xfsm_manager_dbus xfsm-manager-dbus.xml > xfsm-manager-dbus.h
```

## XFCE程序编译

```bash
xdt-autogen
make
```

## ·[错误]possibly undefined macro

**问题**出现 error: possibly undefined macro: AC_PROG_LIBTOOL..错误

**解决方法**

安装libtool包以解决：

```bash
sudo apt-get install libtool
```

## ·[错误]cannot find input file问题

**问题**

```bash
cannot find input file: `po/Makefile.in.in'
```

**解决方法**

```bash
autoreconf -ivf
intltoolize --force --copy
./configure
make
```

若出现`You should update your 'aclocal.m4' by running aclocal.`运行

```bash
intltoolize --force --copy --automake
```

安装intltool

```bash
apt-get install intltool
```

## ·[错误]zh_CN出现无效的多字节序列

**检测**.po文件的编码格式

```bash
"Content-Type: text/plain; charset=ASCII\n"
```

**改为utf-8**

```bash
"Content-Type: text/plain; charset=UTF-8\n"
```

## ·[错误]编译出现HAVE_INTROSPECTION does not appear in AM_CONDITIONAL

**问题**

```bash
error: HAVE_INTROSPECTION does not appear in AM_CONDITIONAL
autoreconf: automake failed with exit status: 1
```

**解决方法**

```bash
apt-get install gobject-introspection
```

## ·[错误]HAVE_INTROSPECTION does not appear in AM_CONDITIONAL

```bash
sudo apt install gobject-introspection
```

## ·[错误]dpkg-buildpackage：failed to sign .buildinfo file

```bash
dpkg-buildpackage -b -tc -us -uc
```

## ·[错误]cups-config not found but CUPS support requested

```bash
sudo apt install libcups2-dev
```

## ·[错误]GNOME DEBUG CHECK command not found

**问题**

```bash
GNOME DEBUG CHECK: command not found
```

**解决方法**

```bash
sudo apt-get install gtk-doc-tools
sudo apt-get install gnome-commom
intltoolize --force --copy --automake
```

## ·[错误]package ‘fcitx-utils’ not found， package ‘fcitx-config’ not found

```bash
sudo apt-get install fcitx-libs-dev
```

## ·[错误]gnome-builder构建的项目出现

```bash
./configure: line 3074: AX_GENERATE_CHANGELOG: command not found
./configure: line 3077: syntax error near unexpected token `no,'
./configure: line 3077: `AX_CHECK_ENABLE_DEBUG(no,'
```

**解决方法**

```bash
sudo apt-get install autoconf-archive
```

## ·[错误]netperf编译报错

```bash
configure.ac:67: error: possibly undefined macro: AC_CHECK_SA_LEN
```

**解决方法**

```bash
apt-get install autotools-dev autoconf automake texinfo
```

## ·[错误]XFCE相关程序编译出错

```bash
Couldn't find include 'libxfce4util-1.0.gir' (search path: '['.', '/usr/share/gir-1.0', '/usr/share', '/usr/share/xubuntu/gir-1.0', '/usr/share/xfce4/gir-1.0', '/usr/local/share/gir-1.0', '/usr/share/gir-1.0', '/usr/share/gir-1.0', '/usr/share/gir-1.0', '/usr/share/gir-1.0']')
make[4]: *** [/usr/share/gobject-introspection-1.0/Makefile.introspection:156: libxfce4panel-2.0.gir] Error 1
```

**解决方法**

```bash
修改debian/rules
添加--disable-introspection
dh_auto_configure -- --disable-silent-rules --enable-gtk2 --enable-gtk-doc --disable-introspection
```

## .ui或.glade文件生成.h文件

```
exo-csource --static --strip-comments --strip-content --name=name_ui name.ui > name_ui.h
```
