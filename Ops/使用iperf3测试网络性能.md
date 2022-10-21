## 常用参数

### 通用

```
-u 使用udp协议
-w 指定TCP窗口大小，默认是8KB
-i sec 以秒为单位显示报告间隔，eg：iperf -c 10.0.0.2 -i 2
-p 指定服务器端使用的端口或客户端所连接的端口eg:iperf -s -p 9999;iperf -c 10.0.0.2 -p 9999
-f [kmKM] 分别表示以Kbits, Mbits, KBytes, MBytes显示报告,默认以Mbits为单位,eg：iperf -c 10.0.0.2 -f K
-l 缓冲区大小，默认是8KB,eg：iperf -c 10.0.0.2 -l 16
-B 绑定一个主机地址或接口（当主机有多个地址或接口时使用该参数）
-C 兼容旧版本（当server端和client端版本不一样时使用）
-M 设定TCP数据包的最大mtu值
-N 设定TCP不延时
```

### 服务器端

```
-s iperf服务器模式:iperf3 -s
-D 以服务方式运行iperf，eg：iperf -s -D
-R 停止iperf服务，针对-D，eg：iperf -s -R
```

### 客户端

```
-c iperf客户端模式:iperf -c 10.0.0.2
-b UDP模式使用的带宽，单位bits/sec。此选项与-u选项相关。默认值是1 Mbit/sec
-t 测试时间，默认10秒,eg：iperf -c 10.0.0.2 -t 5
-d 同时进行双向传输测试
-r 单独进行双向传输测试
-n 指定传输的字节数，eg：iperf -c 10.0.0.2 -n 100000
-F 指定需要传输的文件
-T 指定ttl值
```

## 结果含义

```
Interval  间隔   
Transfer    传送的总数据量   
Bandwidth   带宽大小    
Jitter  波动率
Lost/Total  丢包/总报文数
Datagrams   丢包率
```
