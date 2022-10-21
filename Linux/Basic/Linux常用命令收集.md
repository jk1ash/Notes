## terminal快捷建

```
ctrl+a:光标移到行首。

ctrl+c:杀死当前进程。

ctrl+d:退出当前 Shell。

ctrl+e:光标移到行尾。

ctrl+u: 清除光标前至行首间的所有内容。

ctrl+l:清屏，相当于clear。

ctrl+y: 粘贴或者恢复上次的删除。

Shift + Insert:粘贴。

Ctrl + Insert:复制。
```

## Linux命令链接操作符

**1. 和号操作符 &**

& 的作用是使命令在后台运行。

**2. 分号操作符 ;**

分号操作符使你可以一口气运行几个命令，命令顺序执行。

**3. 与操作符 &&**

如果第一个命令执行成功，与操作符 && 才会执行第二个命令，也就是说，第一个命令退出状态是0。

**4. 或操作符 ||**

或操作符 || 允许你在第一个命令失败的情况下执行第二个命令，第一个命令的退出状态是1。

**5. 非操作符 !**

这个命令会执行除了提供的条件外的所有的语句。

**6. 与或操作符 && – ||**

实际上是‘与’和‘或’操作符的组合。它很像‘if-else‘语句。

**7. 管道操作符 |**

将第一个命令的输出作为第二个命令的输入。

**8. 命令合并操作符{}**

合并两个或多个命令，第二个命令依赖于第一个命令的执行。

**9. 优先操作符()**

这个操作符可以让命令以优先顺序执行。

*_10. 连接符\*_

被用于连接shell中那些太长而需要分成多行的命令。可以在输入一个“\”之后就回车，然后继续输入命令行，直到输入完成。

## 切换shell和zsh

```shell
#切换shell

chsh -s /bin/shell

#切换zsh

chsh -s /bin/zsh
```

## Linux各类系统信息查询

```shell
# 查看系统内核版本
uname -r

# 查看系统架构
uname -p

# 查看系统版本
uame -a

# 查看系统持续时间和负载
uptime

# 查看内存占用
free

# 查看cpu使用情况
top #任务管理器
vmstat #查看cpu、内存等
sar -u [间隔] [次数] #查看cpu使用情况

# 查看磁盘使用
df

# 查看文件大小
du

# 查看当前路径
pwd

# 查看当前用户或组
id
users
groups
```

## mkdir

```shell
-m<目标属性>或--mode<目标属性>   建立目录的同时设置目录的权限；
-p或--parents    若所要建立目录的上层目录目前尚未建立，则会一并建立上层目录；
```

## cd

```shell
cd    进入用户主目录

cd ~  进入用户主目录

cd -  返回进入此目录之前所在的目录

cd ..  返回上级目录

cd ../..  返回上两级目录
```

## ls

```shell
-a  #显示所有档案及目录

-A  #显示除影藏文件“.”和“..”以外的所有文件列表

-C  #多列显示输出结果。

-l  #以长格式显示目录下的内容列表。输出的信息从左到右依次包括文件名，文件类型、权限模式、硬连接数、所有者、组、文件大小和文件的最后修改时间等

-h  #将文件容量以较易读的方式（GB，kB等）列出来

-t  #将文件依建立时间之先后次序列出

-r  #以文件名反序排列并输出目录内容列表

-R  #递归处理，将指定目录下的所有文件及子目录一并处理
```

## dpkg

```shell
-i  #安装软件包

-I  #显示包信息

-r  #删除软件包

-P  #删除软件包的同时删除其配置文件

-L  #显示于软件包关联的文件

-l  #显示已安装软件包列表

-S  #搜索所属的包内容

-s  #获得已经安装在系统中一个特殊包的信息

-x deb dir  #解开deb到某目录

-X deb dir  #解开deb包到某目录并打印包内文件信息

-e deb DEBIAN #解开control

-b dir debname #打包deb包
```

## cp

```shell
-a  将文件的特性一起复制

-p  连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份

-i  若目标文件已经存在时，在覆盖时会先询问操作的进行

-r  递归持续复制，用于目录的复制行为

-u  目标文件与源文件有差异时才会复制
```

## mv

```shell
-f  #force强制的意思，如果目标文件已经存在，不会询问而直接覆盖

-i  #若目标文件已经存在，就会询问是否覆盖
```

## rm

```shell
-f  #就是force的意思，忽略不存在的文件，不会出现警告消息

-i  #互动模式，在删除前会询问用户是否操作

-r  #递归删除，最常用于目录删除，它是一个非常危险的参数
```

## ps

```shell
-A  #所有的进程均显示出来

-a  #不与terminal有关的所有进程

-u  #有效用户的相关进程

-x  #一般与a参数一起使用，可列出较完整的信息

-l  #较长，较详细地将PID的信息列出
```

## top

```shell
top 实时显示进程状态
M 按内存大小排序
C 按CPU大小排序
```

## kill

```shell
kill -signal PID
```

> 1：SIGHUP，启动被终止的进程

2：SIGINT，相当于输入ctrl+c，中断一个程序的进行

9：SIGKILL，强制中断一个进程的进行

15：SIGTERM，以正常的结束进程方式来终止进程

17：SIGSTOP，相当于输入ctrl+z，暂停一个进程的进行


## chmod

```shell
u 表示该文件的拥有者，g 表示与该文件的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是。
+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
r 表示可读取，w 表示可写入，x 表示可执行，X 表示只有当该文件是个子目录或者该文件已经被设定过为可执行。

r=读取属性　　//值＝4
w=写入属性　　//值＝2
x=执行属性　　//值＝1
```

## tar

c代表压缩，x代表解压缩

```shell
tar -cvf xx.tar xx  #仅打包，不压缩！

tar -zcvf xx.tar.gz xx  #打包后，以 gzip 压缩

tar -jcvf xx.tar.bz2 xx #打包后，以 bzip2 压缩

tar -xvf xx.tar.xz  #将tar.xz包解压

tar -zxvf xx.tar.gz    #将tar.gz包解压

tar -jxvf xx.tar.bz2 #/将tar.bz2包解压

-C path 解压到指定目录
```

## mount

```shell
mount  /dev/cdrom /mnt/cdrom     //挂载cdrom`
```

## umount

```shell
umount  /dev/sda1     //通过设备名卸载
umount /mnt/mymount/ //通过挂载点卸载
```

## df

```shell
-a或--all    包含全部的文件系统；

--block-size=<区块大小> 以指定的区块大小来显示区块数目；

-h或--human-readable 以可读性较高的方式来显示信息；

-H或--si 与-h参数相同，但在计算时是以1000 Bytes为换算单位而非1024 Bytes；

-k或--kilobytes  指定区块大小为1024字节；

-l或--local  仅显示本地端的文件系统；

-T或--print-type 显示文件系统的类型；
```

## netstat

```shell
-a  #(all)显示所有选项，默认不显示LISTEN相关

-t  #(tcp)仅显示tcp相关选项

-u  #(udp)仅显示udp相关选项

-n  #拒绝显示别名，能显示数字的全部转化成数字。

-l  #仅列出有在 Listen (监听) 的服務状态

-p  #显示建立相关链接的程序名

-r  #显示路由信息，路由表

-e  #显示扩展信息，例如uid等

-s  #按各个协议进行统计

-c  #每隔一个固定时间，执行该netstat命令。
```

## chown

```shell
chown [-参数]  user[:group] file

#参数：

user : 新的文件拥有者的使用者 ID
group : 新的文件拥有者的使用者组(group)

-c : 显示更改的部分的信息

-f : 忽略错误信息

-h :修复符号链接

-v : 显示详细的处理信息

-R : 处理指定目录以及其子目录下的所有文件
```

## ln

```shell
##
## 软链接：
##
##    1.软链接，以路径的形式存在。类似于Windows操作系统中的快捷方式
##    2.软链接可以 跨文件系统 ，硬链接不可以
##    3.软链接可以对一个不存在的文件名进行链接
##    4.软链接可以对目录进行链接
##
## 硬链接：
##    
##    1.硬链接，以文件副本的形式存在。但不占用实际空间。
##    2.不允许给目录创建硬链接
##    3.硬链接只有在同一个文件系统中才能创建
###
ln [参数][源文件或目录][目标文件或目录]

-b 删除，覆盖以前建立的链接
-d 允许超级用户制作目录的硬链接
-f 强制执行
-i 交互模式，文件存在则提示用户是否覆盖
-n 把符号链接视为一般目录
-s 软链接(符号链接)
-v 显示详细的处理过程
```

## du查看文件和文件大小

```shell
du -sh *
du -sm * | sort -nr #sort -nr按大小排列
du -ah
```

## 查找的各种指令

```shell
grep xxx -r

find / -name "xxx"

which xxx
```

## scp和sshpass实现免密远程拷贝

```shell
sshpass -p user#123 scp ~/workspace/xx generic@172.16.65.75:/home/generic/downloads
```

## 查看用户的uid和gid

```shell
id root
```

## 查看键盘鼠标事件(xwindow下)

```shell
xev
```

## root用户Operation not permitted

```shell
lsattr YourFile
chattr -i YourFile
```

## 开启和关闭swap分区

```shell
#开启
fallocate -l 1G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon swap
#关闭
swapoff swap
```

## 查看和清理僵尸进程

```shell
# 查看是否有僵尸进程
ps -aux | grep defunct
# 得到进程的ID及其父进程ID
ps -ef | grep 'defunct\|PID' | more

# 清理僵尸进程
## 父进程
ps  -ef | grep 'defunct' | grep -v grep | awk '{print $3}' | xargs kill -9
## 子进程
ps  -ef | grep 'defunct' | grep -v grep | awk '{print $2}' | xargs kill -9
```
