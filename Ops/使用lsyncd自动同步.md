## 安装

```
sudo apt-get install lsyncd
```

## 同步本地文件夹

vim /etc/lsyncd/lsyncd.conf

```
settings {
    logfile      = "/var/log/lsyncd/lsyncd.log",
    statusFile   = "/var/log/lsyncd/lsyncd.status",
    #触发条件
    inotifyMode  = "CloseWrite or Modify",
    #最大同步线程
    maxProcesses = 8,
}

sync {
    #本地同步使用rsync
    default.rsync,
    #源文件夹
    source    = "/www/wwwroot/FileRun/data/",
    #目标文件夹
    target    = "/mnt/cosfs/",
    #同步间隔
    delay     = 1,
    #删除目标目录比源目录多余的文件
    delete = true,
    #过滤列表
    excludeFrom = "/etc/lsyncd/exclude.list",
    #rsync设置
    rsync     = {
        binary    = "/usr/bin/rsync",
        archive   = true,
        compress = false,
        verbose   = true
        },
}
```

## 同步远程文件夹

需先配置ssh免密登陆

```
ssh-keygen -t rsa
ssh-copy-id -i ~/.ssh/id_rsa.pub user@host
```

配置lsyncd

```
settings {
    logfile      = "/var/log/lsyncd/lsyncd.log",
    statusFile   = "/var/log/lsyncd/lsyncd.status",
    inotifyMode  = "CloseWrite or Modify",
    maxProcesses = 8,
}
sync {
    default.rsyncssh,
    source = "/root/Downloads/",
    host = "root@159.75.91.213",
    targetdir = "/www/wwwroot/FileRun/data/aria2-downloads/",
    delay = 1,
    rsync = {
        binary = "/usr/bin/rsync",
        archive = true,
        compress = true,
        perms = true,
        whole_file = false
    },
}
```

## lsyncd参数详解

全局设置，即settings

| 变量名 | 类型 | 描述 | 例子 | 默认值 |
| --- | --- | --- | --- | --- |
| logfile | string | 日志文件路径 | "/var/log/lsyncd.log" | none |
| pidfile | string | pid文件路径 | "/tmp/lsyncd.pid" | none |
| statusFile | string | 状态文件路径 | "/tmp/lsyncd.status" | nome |
| nodaemon | bool | 是否是前台模式 | true | false |
| statusInterval | int | 写入状态文件延迟 | 10 | 10 |
| insist | bool | 是否开启容错模式 | true | false |
| inotifyMode | string | 指定监控事件 | "CloseWrite" or "CloseWrite or Modify" | "CloseWrite" |
| maxProcesses | int | 最大同步进程数 | 8 | 1 |
| maxDelays | int | 最大同步文件数 | 1000 | 1000 |


sync设置，可设置多个

```
default.rsync ：
  使用rsync进行本地目录间同步，或使用rsync的ssh形式进行远程同步，
  或使用rsync的daemon方式进行远程同步（需要目标主机也安装rsync）

default.direct ：
  本地目录间同步，使用cp、rm等命令完成差异文件备份

default.rsyncssh ：
  同步到远程主机目录,使用rsync的ssh模式，需要先配置ssh免密登录（密钥登录）

source：
  同步的源目录，使用绝对路径。

target：
  目标地址，仅用于direct, rsync模式, 对应不同的模式有几种写法例如
  1. /tmp/dest
    本地目录同步  root@172.16.1.31:/opt/
    使用远程shell(如rsh、ssh) 同步到远程服务器目录，
    root@可以省略，默认是root用户 。用于rsync模式。
  2. root@172.29.88.223::var/back
    使用rsync的daemon模式同步到远程服务器目录，目标主机也需要安装rsync。
    var/back等于/var/back，root@可以省略，默认是root用户。
    用于rsync模式。

targetdir
  目标地址，仅用于rsyncssh模式，例如：/tmp/dest

init
  当init = false，只同步进程启动以后发生改动事件的文件，原有的目录即使有差异也不会同步。
  默认是true

delay
  等待rsync同步延时时间，默认15秒（最大累计到1000个不可合并的事件
  （settings中的maxDelays，默认1000）。也就是15s内监控目录下发生的改动，
  会累积到一次rsync同步，避免过于频繁的同步。
  （可合并的意思是，15s内两次修改了同一文件，最后只同步最新的文件）

excludeFrom
  从文件加载排除规则，如excludeFrom = "/etc/lsyncd.exclude"

exclude
  从字符串加载排除规则，如exclude = { '*.bak' , '*.tmp' }

这里的规则写法与原生rsync有点不同，更为简单：
如果规则以斜线/开头，则从头开始要匹配全部
如果规则以/结尾，则要匹配监控路径的末尾
?匹配任何字符，但不包括/
*匹配0或多个字符，但不包括/
**匹配0或多个字符，可以是/

delete
  为了保持target与souce完全同步，Lsyncd默认会delete = true来允许同步删除。
  它除了false，还有startup、running值。
  false
    Lsyncd不会删除目标上的任何文件。既不在启动时也不在正常运行时。（但是可能会覆盖）
'startup'
    Lsyncd在启动时会删除目标上的文件，但在正常运行时不会删除。
'running'
    Lsyncd在启动时不会删除目标上的文件，但会删除在正常操作过程中删除的文件。

rsync
  配置rsync的命令行参数。 rsync参数太多了，大家看下官方文档把。
  仅适用于，default.rsyncssh和default.rsync模式

host
  远程主机地址，例如: 192.168.1.1 或者 zhai@192.168.8.1.1

ssh
  配置ssh参数，例如端口：port=1234
```

## rsync参数详解

```
-v, --verbose 详细模式输出。
-q, --quiet 精简输出模式。
-c, --checksum 打开校验开关，强制对文件传输进行校验。
-a, --archive 归档模式，表示以递归方式传输文件，并保持所有文件属性，等于-rlptgoD。
-r, --recursive 对子目录以递归模式处理。 -R, --relative 使用相对路径信息。
-b, --backup 创建备份，也就是对于目的已经存在有同样的文件名时，将老的文件重新命名为~filename。可以使用--suffix选项来指定不同的备份文件前缀。
--backup-dir 将备份文件(如~filename)存放在在目录下。
-suffix=SUFFIX 定义备份文件前缀。
-u, --update 仅仅进行更新，也就是跳过所有已经存在于DST，并且文件时间晚于要备份的文件，不覆盖更新的文件。
-l, --links 保留软链结。
-L, --copy-links 想对待常规文件一样处理软链结。
--copy-unsafe-links 仅仅拷贝指向SRC路径目录树以外的链结。
--safe-links 忽略指向SRC路径目录树以外的链结。
-H, --hard-links 保留硬链结。
-p, --perms 保持文件权限。
-o, --owner 保持文件属主信息。
-g, --group 保持文件属组信息。
-D, --devices 保持设备文件信息。
-t, --times 保持文件时间信息。
-S, --sparse 对稀疏文件进行特殊处理以节省DST的空间。
-n, --dry-run现实哪些文件将被传输。
-w, --whole-file 拷贝文件，不进行增量检测。
-x, --one-file-system 不要跨越文件系统边界。
-B, --block-size=SIZE 检验算法使用的块尺寸，默认是700字节。
-e, --rsh=command 指定使用rsh、ssh方式进行数据同步。
--rsync-path=PATH 指定远程服务器上的rsync命令所在路径信息。
-C, --cvs-exclude 使用和CVS一样的方法自动忽略文件，用来排除那些不希望传输的文件。
--existing 仅仅更新那些已经存在于DST的文件，而不备份那些新创建的文件。
--delete 删除那些DST中SRC没有的文件。
--delete-excluded 同样删除接收端那些被该选项指定排除的文件。
--delete-after 传输结束以后再删除。
--ignore-errors 及时出现IO错误也进行删除。
--max-delete=NUM 最多删除NUM个文件。
--partial 保留那些因故没有完全传输的文件，以是加快随后的再次传输。
--force 强制删除目录，即使不为空。
--numeric-ids 不将数字的用户和组id匹配为用户名和组名。
--timeout=time ip超时时间，单位为秒。
-I, --ignore-times 不跳过那些有同样的时间和长度的文件。
--size-only 当决定是否要备份文件时，仅仅察看文件大小而不考虑文件时间。
--modify-window=NUM 决定文件是否时间相同时使用的时间戳窗口，默认为0。
-T --temp-dir=DIR 在DIR中创建临时文件。
--compare-dest=DIR 同样比较DIR中的文件来决定是否需要备份。
-P 等同于 --partial。
--progress 显示备份过程。
-z, --compress 对备份的文件在传输时进行压缩处理。
--exclude=PATTERN 指定排除不需要传输的文件模式。
--include=PATTERN 指定不排除而需要传输的文件模式。
--exclude-from=FILE 排除FILE中指定模式的文件。
--include-from=FILE 不排除FILE指定模式匹配的文件。
--version 打印版本信息。
--address 绑定到特定的地址。
--config=FILE 指定其他的配置文件，不使用默认的rsyncd.conf文件。
--port=PORT 指定其他的rsync服务端口。
--blocking-io 对远程shell使用阻塞IO。
-stats 给出某些文件的传输状态。
--progress 在传输时现实传输过程。
--log-format=formAT 指定日志文件格式。
--password-file=FILE 从FILE中得到密码。
--bwlimit=KBPS 限制I/O带宽，KBytes per second。
-h, --help 显示帮助信息。
```

## aria2同步

aria2下载过程中，为了防止下载临时文件也sync，做出以下设置

创建一个临时目录Downloads和一个最终文件目录Backup

aria2.conf添加

```
dir=/root/Downloads
on-download-complete=/etc/aria2/aria2-done.sh
```

aria2-done.sh脚本

```
#!/bin/bash
mv /root/Downloads/* /root/Backup/
chmod 777 /root/Backup/*
rm /root/Downloads/* -rf
```

chmod +x /etc/aria2/aria2-done.sh

lsyncd同步目录选择Backup即可
