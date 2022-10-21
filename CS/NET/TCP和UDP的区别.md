## TCP(传输控制协议)与UDP(用户数据报协议)基本区别

1. TCP基于连接，UDP基于无连接
2. TCP要求系统资源较多，UDP较少；
3. TCP复杂，UDP程序结构较简单；
4. TCP保证数据正确性，UDP可能丢包；
5. TCP保证数据顺序，UDP不保证；
6. TCP速度慢，UDP速度快

## TCP应用场景

当对网络通讯质量有要求的时候，比如：整个数据要准确无误的传递给对方，这往往用于一些要求可靠的应用，比如HTTP、HTTPS、FTP等传输文件的协议，POP、SMTP等邮件传输的协议。

在日常生活中，常见使用TCP协议的应用如下：

浏览器，用的HTTP

FlashFXP，用的FTP

Outlook，用的POP、SMTP

Putty，用的Telnet、SSH

QQ文件传输

...

## UDP应用场景

1. 面向数据报方式
2. 网络数据大多为短消息
3. 拥有大量Client
4. 对数据安全性无特殊要求
5. 网络负担非常重，但对响应速度要求高

比如，日常生活中，常见使用UDP协议的应用如下：

QQ语音

QQ视频

TFTP

...

## 具体编程时的区别

1. socket()的参数不同
2. UDP Server不需要调用listen和accept
3. UDP收发数据用sendto/recvfrom函数
4. TCP：地址信息在connect/accept时确定
5. UDP：在sendto/recvfrom函数中每次均 需指定地址信息
6. UDP：shutdown函数无效

## TCP编程

### 服务器端一般步骤

1. 创建一个socket，用函数socket()；
2. 设置socket属性，用函数setsockopt(); * 可选
3. 绑定IP地址、端口等信息到socket上，用函数bind();
4. 开启监听，用函数listen()；
5. 接收客户端上来的连接，用函数accept()；
6. 收发数据，用函数send()和recv()，或者read()和write();
7. 关闭网络连接；
8. 关闭监听；

### 客户端一般步骤

1. 创建一个socket，用函数socket()；
2. 设置socket属性，用函数setsockopt();* 可选
3. 绑定IP地址、端口等信息到socket上，用函数bind();* 可选
4. 设置要连接的对方的IP地址和端口等属性；
5. 连接服务器，用函数connect()；
6. 收发数据，用函数send()和recv()，或者read()和write();
7. 关闭网络连接；

## UDP编程

### 服务器端一般步骤

1. 创建一个socket，用函数socket()；
2. 设置socket属性，用函数setsockopt();* 可选
3. 绑定IP地址、端口等信息到socket上，用函数bind();
4. 循环接收数据，用函数recvfrom();
5. 关闭网络连接；

### 客户端一般步骤

1. 创建一个socket，用函数socket()；
2. 设置socket属性，用函数setsockopt();* 可选
3. 绑定IP地址、端口等信息到socket上，用函数bind();* 可选
4. 设置对方的IP地址和端口等属性;
5. 发送数据，用函数sendto();
6. 关闭网络连接；
