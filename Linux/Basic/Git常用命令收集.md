## git配置
### git配置用户名和邮箱

```shell
git config --global user.name "wang-jj"
git config --global user.email wang-jj@itep.com.cn
```

### 修改git编辑器为vscode

```shell
git config --global core.editor Code
```

### 修改git编辑器为vim

```shell
git config --global core.editor vim
git config --global merge.tool vimdiff
```

### 测试密钥连接

```shell
ssh -T git@github.com
```

### ssh生成新的密钥

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

### 配置远程仓库host

vim ~/.ssh/config

```shell
host gitlab
    user git
    hostname 172.16.70.17
    port 10022
    identityfile ~/.ssh/id_rsa
```

### 批量替换远程仓库地址

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

## 新建代码库

```shell
# 在当前目录新建一个Git代码库
git init

# 新建一个目录，将其初始化为Git代码库
git init [project-name]

# 下载一个项目和它的整个代码历史
git clone [url]
```

## 增加/删除文件

```shell
# 添加指定文件到暂存区
git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
git add [dir]

#增加当前子目录下所有更改过的文件至暂存区
git add .

# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
git add -p

# 删除工作区文件，并且将这次删除放入暂存区
git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
git mv [file-original] [file-renamed]
```

## 代码提交

```shell
# 提交暂存区到仓库区
git commit -m [message]

# 提交暂存区的指定文件到仓库区
git commit [file1] [file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
git commit -a

# 提交时显示所有diff信息
git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
git commit --amend [file1] [file2] ...
```

## 分支操作

```shell
# 列出所有本地分支
git branch

# 列出所有远程分支
git branch -r

# 列出所有本地分支和远程分支
git branch -a

# 新建一个分支，但依然停留在当前分支
git branch [branch-name]

# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 新建一个分支，指向指定commit
git branch [branch] [commit]

# 新建一个分支，与指定的远程分支建立追踪关系
git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区
git checkout [branch-name]

# 切换到上一个分支
git checkout -

# 建立追踪关系，在现有分支与指定的远程分支之间
git branch --set-upstream-to [remote-branch]

# 合并指定分支到当前分支
git merge [branch]
(强制merge加上--allow-unrelated-histories，并用git commit合并冲突)

# 选择一个commit，合并进当前分支
git cherry-pick [commit]

# 删除分支
git branch -d [branch-name]

# 删除远程分支
git push origin --delete [branch-name]
git branch -r -d [remote/branch]

# 分支重命名
git branch -m old new

# 合并分支
git rebase [branch]
```

## 远程仓库操作

```shell
# 下载远程仓库的所有变动
git fetch [remote]

# 显示所有远程仓库
git remote -v

# 显示某个远程仓库的信息
git remote show [remote]

# 增加一个新的远程仓库，并命名
git remote add [shortname] [url]

# 取回远程仓库的变化，并与本地分支合并
git pull [remote] [branch]

# 上传本地指定分支到远程仓库
git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
git push [remote] --force

# 推送所有分支到远程仓库
git push [remote] --all

#查看远程仓库
git remote -v

# 连接远程仓库
git remote add origin [url]

#删除已连接的远程仓库
git remote rm origin

#修改已连接的远程仓库
git remote set-url --push origin [newUrl]

#推送当前分支并建立与远程上游的跟踪
git push --set-upstream origin develop
```

## 查看信息

```shell
# 显示有变更的文件
git status

# 显示当前分支的版本历史
git log

# 显示commit历史，以及每次commit发生变更的文件
git log --stat

# 搜索提交历史，根据关键词
git log -S [keyword]

# 显示某个commit之后的所有变动，每个commit占据一行
git log [tag] HEAD --pretty=format:%s

# 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
git log [tag] HEAD --grep feature

# 显示某个文件的版本历史，包括文件改名
git log --follow [file]
git whatchanged [file]

# 显示指定文件相关的每一次diff
git log -p [file]

# 显示过去5次提交
git log -5 --pretty --oneline

# 显示所有提交过的用户，按提交次数排序
git shortlog -sn

# 显示指定文件是什么人在什么时间修改过
git blame [file]

# 显示暂存区和工作区的差异
git diff

# 显示暂存区和上一个commit的差异
git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
git diff HEAD

# 显示两次提交之间的差异
git diff [first-branch]...[second-branch]

# 显示今天你写了多少行代码
git diff --shortstat "@{0 day ago}"

# 显示某次提交的元数据和内容变化
git show [commit]

# 显示某次提交发生变化的文件
git show --name-only [commit]

# 显示某次提交时，某个文件的内容
git show [commit]:[filename]

# 显示当前分支的最近几次提交
git reflog
```

## 撤销操作

```shell
# 恢复暂存区的指定文件到工作区
git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
git checkout .

# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
git reset --keep [commit]

# 新建一个commit，用来撤销指定commit
# 后者的所有变化都将被前者抵消，并且应用到当前分支
git revert [commit]

# 暂时将未提交的变化移除，稍后再移入
git stash
git stash pop

# 删除工作区的修改
git clean -fd
```

## 标签操作

```shell
# 查看本地分支标签
git tag
git tag -l
git tag --list

# 查看远程所有标签
git ls-remote --tags
git ls-remote --tag

# 给当前分支打标签
git tag v1.1.0

# 给特定的某个commit版本打标签
git tag v1.0.0 039bf8b

# 删除本地某个标签
git tag --delete v1.0.0
git tag -d v1.0.0

# 删除远程的某个标签
git push -d origin v1.0.0
git push --delete origin v1.0.0

# 将本地标签一次性推送到远程
git push origin --tags
git push origin --tag

# 将本地某个特定标签推送到远程
git push origin v1.0.0

# 查看某一个标签的提交信息
git show v1.0.0

# 重命名标签名
git tag new old
git tag -d old

# 拉取远程tag
git fetch origin --prune
git fetch origin v1.0.0
```

## 删除最后一次提交

```shell
git revert HEAD
git push origin master
```

## 打补丁

```shell
#创建某次提交（含）之前的几次提交的patch
git format-patch -n [commit sha1 id]

#某两次提交之间的所有patch
git format-patch [commit sha1 id]..[commit sha1 id]

#某次提交的patch
git format-patch [commit sha1 id]~1..[commit sha1 id]

#创建diff文件的常用方法
git diff [commit sha1 id] [commit sha1 id] >  [diff文件名]

#检查patch/diff是否能正常打入
git apply --check [path/to/xxx.patch]
git apply --check [path/to/xxx.diff]

#打入patch/diff
git apply [path/to/xxx.patch]
git apply [path/to/xxx.diff]
```

## 提交规范
```
<类型>[可选的作用域]: <描述>

[可选的正文]

[可选的脚注]
```
**类型定义：**
- feat: 新功能、新特性
- fix: 修改 bug
- perf: 更改代码，以提高性能（在不影响代码内部行为的前提下，对程序性能进行优化）
- refactor: 代码重构（重构，在不影响代码内部行为、功能下的代码修改）
- docs: 文档修改
- style: 代码格式修改, 注意不是 css 修改（例如分号修改）
- test: 测试用例新增、修改
- build: 影响项目构建或依赖项修改
- revert: 恢复上一次提交
- ci: 持续集成相关文件修改
- chore: 其他修改（不在上述类型中的修改）
- release: 发布新版本
- workflow: 工作流相关文件修改