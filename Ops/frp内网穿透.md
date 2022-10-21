## 服务端

### 安装配置

```shell
#下载解压
tar -zxvf frp.xxx.tar.gz

#安装
cd frp.xxx
cp frps /usr/bin/
cp frps.ini /etc/frp/

#设置开机启动
cp systemd/frps.service /usr/lib/systemd/system/
systemctl enable frps.service

#启动
systemctl start frps.service
```

### 配置文件参数

```shell
[common]
bind_addr = 0.0.0.0 #绑定IP
bind_port = 7000   #绑定端口
bind_udp_port = 7001 #绑定udp端口

# 仪表盘
# IP 与 bind_addr 默认相同，可以不设置
# dashboard_addr = 0.0.0.0
#仪表盘端口必须设置
dashboard_port = 7500
# 仪表盘用户和密码
dashboard_user = admin
dashboard_pwd = admin

# 用于 kcp 协议的 udp 端口，可以与'bind_port'相同。如果未设置，则frps禁用kcp
kcp_bind_port = 7000

# 日志文件路径
log_file = /var/log/frps.log
# 设置日志显示级别{debug, info, warn, error)
log_level = info
# 设置日志最大天数
log_max_days = 3

# frp身份验证
token = 12345678

# 端口白名单
allow_ports = 2000-3000,3001,3003,4000-50000

# 指定要侦听的地址代理，默认值与 bind_addr 相同，127.0.0.1仅限本地、0.0.0.0为不限地址
# proxy_bind_addr = 127.0.0.1

# 如果要支持虚拟主机，则必须设置用于侦听的http端口（可选）
# 注意：http 端口和 https 端口可以与 bind_port 相同
# vhost_http_port = 80
# vhost_https_port = 443
# vhost http 服务器的响应标头超时（秒），默认为60s
# vhost_http_timeout = 60
```

## linux客户端

### 安装配置

```shell
#下载解压
tar -zxvf frp.xxx.tar.gz

#安装
cd frp.xxx
cp frpc /usr/bin/
cp frpc.ini /etc/frp/

#设置开机启动
cp systemd/frpc.service /usr/lib/systemd/system/
systemctl enable frpc.service

#启动
systemctl start frpc.service
```

### 配置文件参数

```shell
[common]
server_addr = 127.0.0.1 #frp服务器地址(IP或者域名)
server_port = 7000 #frp绑定端口
token = 123 #frp密钥

#设置要转发的服务
[ssh] #服务名称
type = tcp #协议类型
local_ip = 127.0.0.1 #地址为本地
local_port = 22 #端口
remote_port = 6000 #远程转发端口(服务端需开放该端口，不然会连接失败)
```

**示例:**

```shell
[common]
server_addr = jklash.com
server_port = 7000
token=jkl0079

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 10000
```

远程可通过10000端口进行ssh连接

```shell
ssh -p 10000 root@jklash.com
```
