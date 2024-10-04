## 常用命令

```shell
#列出所有已加载的单元
systemctl list-units

#查看指定的单元的配置
systemctl cat [serviceName]

#修改指定单元的配置
systemctl edit [serviceName]

#启动/重启/停止指定单元
systemctl start/restart/stop [serviceName]

#查看指定单元状态
systemctl status [serviceName]

#启用/禁止指定单元
systemctl enable/disable [serviceName]

#重载配置
systemctl daemon-reload
```

## 配置文件

### [Unit]

- `Description`: 当前服务的简易说明
- `Documentation`: 文档位置（以空格分隔）

   - 该项可以是网页链接，也可以是 manpages 的名称，亦或是文件路径。
- `Before`: 在哪些服务之前启动

   - 本字段不涉及依赖关系，只是说明了启动顺序
- `After`: 在哪些服务之后启动

   - 本字段不涉及依赖关系，只是说明了启动顺序
   - 以 `sshd.service` 中的配置为例，该服务需要在 `network.target` 和 `auditd.service` 之后启动
- `Wants`: 弱依赖的服务

   - 若被依赖的服务被停止，这个服务不需要停止
- `Requires`: 强依赖的服务

   - 若被依赖的服务没有启动，则不能启动这个服务
   - 若被依赖的服务被停止，则这个服务也必须停止
- `Conflicts`: 冲突的服务

   - 如果列出的服务中有一个已经运行，那么就不能启动这个服务

### [Service]

- `Type`: 启动类型。默认值为 `simple` ，可选值如下：

   - `simple`: 使 `ExecStart` 项启动的项成为主进程
   - `forking`: `ExecStart` 项将会以 `fork()` 的形式启动，此时父进程将会退出，子进程将成为主进程
   - `oneshot`: 类似于 `simple` ，但只执行一次，Systemd 会等它执行完，才启动其他服务
   - `dbus`: 类似于 `simple` ，但会等待 `D-Bus` 信号后启动
   - `notify`: 类似于 `simple` ，启动结束后会发出通知信号，然后 Systemd 再启动其他服务
   - `idle`: 类似于 `simple` ，但是要等到其他任务都执行完，才会启动该服务。

      - 这个选项的其中一种使用场合是为让该服务的输出，不与其他服务的输出相混合
      - 这个选项的另外一种使用场合是执行只需要再开机的时候执行一次的程序
- `Environment`: 指定环境变量
- `EnvironmentFile`: 环境变量配置文件，该文件内部的 `key=value` 形式的配置可以在当前文件中以 `$key` 获取
- `ExecStart`: 服务启动时执行的命令
- `ExecReload`: 服务重启时执行的命令
- `ExecStop`: 服务停止时执行的命令
- `ExecStartPre`: 服务启动之前执行的命令
- `ExecStartPost`: 服务启动之后执行的命令
- `ExecStopPost`: 服务停止之后执行的命令
- `Restart`: 服务退出后的重启方式，默认值为 `no`

   - `no`: 进程退出后不会重启
   - `on-success`: 当进程正常退出时（退出状态码为 0）重启
   - `on-failure`: 当进程非正常退出时（退出状态码不为 0、被信号终止、程序超时）重启
   - `on-abnormal`: 当进程被信号终止或程序超时时重启
   - `on-abort`: 当收到没有捕捉到的信号终止时重启
   - `on-watchdog`: 当进程超时退出时重启
   - `always`: 总是重启（不论原因）
   - 对于守护进程，推荐设为 `on-failure`。对于那些允许发生错误退出的服务，可以设为 `on-abnormal`。
- `RemainAfterExit`: 退出后是否重新启动

   - 当设定为 `RemainAfterExit=1` 时，则当这个服务所属的所有程序都终止之后，此服务会再尝试启动。这对于 `Type=oneshot` 的服务很有帮助
- `TimeoutSec`: 当这个服务在启动或停止时失败进入"强制结束"状态的等待秒数。
- `KillMode`: 定义 Systemd 如何停止这个服务，默认值为 `control-group`

   - `control-group`: 服务停止时关闭此控制组中所有的进程
   - `process`: 服务停止时只终止主进程（ExecStart 接的后面那串指令）
   - `mixed`: 主进程将收到 **SIGTERM** 信号，子进程收到 **SIGKILL** 信号
   - `none`: 没有进程会被杀掉，只是执行服务的 stop 命令
- `RestartSec`: 表示 Systemd 重启服务之前，需要等待的秒数（默认是 100ms）
- _注：所有的启动设置之前，都可以加上一个连词号 (**-**) ，表示 「抑制错误」 ，即发生错误的时候，不影响其他命令的执行。_*

### [Install]

- `WantedBy`: 表示该服务所在的 Target

   - 一般来说，服务性质的单元都是挂在 `multi-user.target` 下的
- `Also`: 当该服务被启用时需要同时启用的单元
- `Alias`: 指定创建软链接时链接至本单元配置文件的别名文件
