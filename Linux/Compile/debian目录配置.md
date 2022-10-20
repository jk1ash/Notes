# debian目录配置

## 命令

```
dh_make -p package_version -n -s -e email
```

可在目录下生成得debian目录

## 指定data包安装目录位置

- debian下新建一个data目录和xxx.install文件
- 将data路径（/debian/data）
- 加入xxx.install文件中，并指定安装目录例：/debian/data/demo
- /usr/share/demo 则安装到/usr/share的demo路径下

如果找不到该命令，安装

```
apt-get install dh-make
```

## 如何编辑changelog

命令dch 可编辑changelog
dch -r可发布版本，变为release版本

## dch所属包

sudo apt-get install debian-builder
