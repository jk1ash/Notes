# svn常用命令收集

## 检出checkout

```shell
svn  checkout  http:// 路径(目录或文件的全路径) [本地目录全路径] --username 用户名 --password 密码

svn  co http:// 路径(目录或文件的全路径) [本地目录全路径] --username 用户名 --password 密码
```

## 更新update

```shell
svn update  #更新当前目录及子目录文件到最新版本

svn update -r 200 test  #test文件还原到版本200

svn up  #简写
```

## 导出export

导出一个干净的不带.svn文件夹的目录树

```shell
svn  export  [-r 版本号]  http://路径(目录或文件的全路径)[本地目录全路径] --username 用户名

svn  export  [-r 版本号]  svn://路径(目录或文件的全路径)[本地目录全路径] --username 用户名

svn  export 本地检出的(即带有.svn文件夹的)目录全路径 要导出的本地目录全路径
```

## 添加add

```shell
svn add file1   #添加file1.php
svn add *   #添加当前目录下所有文件
```

## 提交commit

```shell
svn commit -m "message" 
svn ci  #简写
```

## 查看文件或目录状态

```shell
svn status path
svn st      #简写
```

## 删除

```shell
svn delete path -m "comment"

#或

svn delete file 
svn ci -m "comment"
```

## 查看文件或者目录状态

```shell
svn st  目录路径/名

svn status
```

## 查看日志

```shell
svn log path
```

## 查看文件信息

```shell
svn info path
```

## 比较差异

```shell
svn diff path

svn diff -r 200:201 file    # 版本200 和 201 比较

svn di  #简写
```

## 合并

```shell
svn merge -r v1:v2 path
```

## 查看帮助

```shell
svn help
```

## 查看版本库下的文件和目录列表

```shell
svn list svn:// 路径(目录或文件的全路径)

svn ls svn://
```

## 加锁/解锁

```shell
svn lock -m “加锁备注信息文本“ [--force] 文件名 

svn unlock
```
