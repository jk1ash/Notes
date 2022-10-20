# Git&SSH配置

## git配置用户名和邮箱

```shell
git config --global user.name "wang-jj"
git config --global user.email wang-jj@itep.com.cn
```

## 修改git编辑器为vscode

```shell
git config --global core.editor Code
```

## 修改git编辑器为vim

```shell
git config --global core.editor vim
git config --global merge.tool vimdiff
```

## 测试密钥连接

```shell
ssh -T git@github.com
```

## ssh生成新的密钥

```shell
# -f指定文件名
# -C指定公钥备注
ssh-keygen -t rsa -C "jklash1976@gmail.com" -f test
```

上述命令回车后，弹出下面提示，可设置密码，不需要密码直接回车即可

```shell
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

## 配置远程仓库host

vim ~/.ssh/config

```shell
host gitlab
    user git
    hostname 172.16.70.17
    port 10022
    identityfile ~/.ssh/id_rsa
```

## 批量替换远程仓库地址

```shell
#!/bin/shell
directoryContainingAllRepos=$(pwd) # directory containing all git repo's
oldGitRemoteServer="xxx" # current remote server url for example gitlab.com
newGitRemoteServer="xxx" # new remote server url for example git.example.com
cd $directoryContainingAllRepos
find * -maxdepth 0 -type d \( ! -name . \) -print | while read dir #递归一层目录
do
    cd $dir
    if [ -d ".git" ]
    then
        remoteUrl1="$(git config --get remote.origin.url)"
        remoteUrl2=${remoteUrl1/$oldGitRemoteServer/$newGitRemoteServer}
        remoteUrl3=${remoteUrl2/-module}
        git remote set-url origin ${remoteUrl3}
    fi
    cd ..
done
```
