## 安装

安装脚本安装

```
curl -fsSL https://code-server.dev/install.sh | sh
```

或者下载安装包安装

[https://github.com/cdr/code-server/releases](https://github.com/cdr/code-server/releases)

```
sudo dpkg -i code-server_3.11.0_amd64.deb
```

## 开机启动

```
systemctl enable --now code-server@$USER
```

## 配置

修改~/.config/code-server/config.yaml

```bash
bind-addr: 0.0.0.0:10080 #修改地址和端口，地址修改为0.0.0.0，端口随意
#无密码模式 auth: none
auth: password
password: wjj.1976
cert: false
```

nginx反代设置

```
#PROXY-START/

  location / { # Or / if hosting at the root.
      proxy_pass http://localhost:8080/;
      proxy_set_header Host $host;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection upgrade;
      proxy_set_header Accept-Encoding gzip;
  }

#PROXY-END/
```
