## Linux环境安装

```shell
#下载
wget https://dl.google.com/go/go1.16.4.linux-amd64.tar.gz

# 安装
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.16.4.linux-amd64.tar.gz

#配置环境变量
#修改~/.zshrc或~/.bashrc,添加
export PATH=$PATH:/usr/local/go/bin

#查看是否配置成功
go version
#输出下列则表示配置成功
go version go1.16.3 linux/amd64
```

## 配置国内仓库

```shell
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```

## 安装gopls代码补全工具

```shell
go get -v golang.org/x/tools/gopls
```

## 安装gocomplete命令自动补全工具

```shell
go get -u github.com/posener/complete/gocomplete
#安装
gocomplete -install
#卸载
gocomplete -uninstall
```
