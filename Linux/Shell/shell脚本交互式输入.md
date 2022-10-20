## 重定向

```bash
./test <<EOF
yes
EOF
```

## expect

需要安装expect包

```bash
#!/bin/expect
set timeout 30
spawn ssh -l test 10.125.25.189
expect "password:"
send "test123\r"
# interact代表执行完留在远程控制台，不加这句执行完后返回本地控制台
interact
```
