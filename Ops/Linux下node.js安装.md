# Linux下node.js安装

## 安装nvm

```
wget https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh
chmod +x install.sh
./install.sh

#或者
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash

#zsh加入插件nvm
source ~/.zshrc
```

## 使用淘宝源

```
echo -e "\nexport NVM_NODEJS_ORG_MIRROR=http://npm.taobao.org/mirrors/node" >> ~/.zshrc

source ~/.zshrc
```

## 安装nodejs和npm

查询nodejs版本列表

```
nvm ls-remote
```

安装指定版本

```
nvm install v14.15.5
```

使用指定版本

```
nvm use v14.15.5
```

查看本地版本列表和当前版本

```
nvm list
```

## npm命令

切换淘宝源

```
npm config set registry https://registry.npm.taobao.org
# 查看源地址
npm config get registry
```

安装卸载指定nodejs包

```
nmp install xxx -g
nmp uninstall xxx
```

查看安装

```
#查看所有包
npm list -g --depth

#查看指定包
npm list xxx
```

更新npm到最新版本

```
npm install npm@latest -g
```
