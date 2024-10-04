## CMD快捷指令

- control   打开控制面板
- mstsc 打开远程桌面
- notepad   打开记事本
- lusrmgr.msc   打开用户和组
- msconfig  打开系统配置
- calc  打开计算器
- regedit   打开注册表
- taskmgr   打开任务管理器
- services.msc  打开服务
- gpedit.msc    打开组策略
- powershell    打开powershell
- perfmon.msc   打开性能监视器
- netplwiz  打开用户账户

## wsl设置

[官方文档](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package)

```powershell
#启用wsl
Enable-WindowsOptionalFeature  -Online  -FeatureName  VirtualMachinePlatform
Enable-WindowsOptionalFeature  -Online  -FeatureName  Microsoft-Windows-Subsystem-Linux
#查询当前wsl列表
wsl -l
#查看详细信息
wsl --list --verbose
#默认设置wsl2
wsl --set-default-version 2
#设置单个
wsl  --set-version  Ubuntu  2
#设置wsl默认root登录
ubuntu.exe config --default-user root

#查看wsl文件系统
#地址栏输入\\wsl$

#[错误]wsl2请启用虚拟机平台 Windows 功能并确保在 BIOS 中启用虚拟化
#启用hyper-v
bcdedit /set hypervisorlaunchtype auto

#[错误]wsl2参考的对象类型不支持尝试的操作
netsh winsock reset
```
