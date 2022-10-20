# Ubuntu常见编译环境安装

## 安装gcc

```
sudo apt-get install gcc
```

## 安装g++编译器，可以通过命令

```
sudo apt-get install build-essential
```

## 安装debug工具（可选）

```
sudo apt-get install gdb
```

## 安装GTK+3.0

```
sudo apt-get install libgtk-3-dev
```

## 安装pkg-config

```
sudo apt-get install pkg-config
```

## Qt环境配置

```
sudo apt-get install qtbase5-dev

sudo apt-get install qttools5-dev-tools

sudo apt-get install qttools5-dev

sudo apt-get install qt5-default

sudo apt-get install qtcreator
```

## 安装中文包

```
sudo apt-get install  language-pack-zh-hans
sudo apt-get install  language-pack-zh-hans-base
sudo apt-get install  language-pack-zh-hant
sudo apt-get install  language-pack-zh-hant-base
```

## 安装中文字体

```
sudo apt install -y --force-yes --no-install-recommends fonts-wqy-microhei

sudo apt install -y --force-yes --no-install-recommends ttf-wqy-zenhei
```

## 安装alsa模块

```
# 清除系统原有的alsa模块
sudo apt-get remove linux-sound-base alsa-base alsa-utils
# 安装alsa模块
sudo apt-get install xmlto libasound2-dev  //安装alsa-lib
sudo apt-get install linux-sound-base alsa-base alsa-utils
```
