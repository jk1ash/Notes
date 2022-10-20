# Linux环境变量设置

## 临时

使用export命令设置，只对当前shell起作用，将在关闭shell时失效

export PATH=/usr/local/share

## 永久

### 当前用户

修改~/.bashrc

立即生效 source ~/.bashrc

### 全部用户

修改/etc/profile
