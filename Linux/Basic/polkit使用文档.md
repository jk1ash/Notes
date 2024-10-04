
> polkit 是一个应用程序级工具包，用于定义和处理允许非特权进程与特权进程对话的策略：它是一个框架，用于集中决策过程，以授予非特权应用程序对特权操作的访问权限。Polkit 用于控制系统范围的权限。它为非特权进程与特权进程通信提供了一种有组织的方式。与 sudo 等系统相比，它不会向整个进程授予 root 权限，而是允许对集中式系统策略进行更精细的控制。


## 配置文件

Actions:`/usr/share/polkit-1/action`

Authorization rules:`/usr/share/polkit-1/rules.d` or `/etc/polkit-1/rules.d`

### Actions:

**模版：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE policyconfig PUBLIC
 "-//freedesktop//DTD PolicyKit Policy Configuration 1.0//EN"
 "http://www.freedesktop.org/standards/PolicyKit/1/policyconfig.dtd">
<policyconfig>
  <vendor>Deepin</vendor>
  <vendor_url>https://www.deepin.com</vendor_url>
  <!-- 权限ID，这个必须唯一 -->
  <action id="com.deepin.pkexec.dde-file-manager">
    <icon_name>folder</icon_name>
    <!-- 英文弹窗提示 -->
    <message>Authentication is required to run the Deepin File Manager</message>
    <!-- 语言为简体中文时的弹窗提示 -->
    <message xml:lang="zh_CN">查看文件夹需要输入密码</message>
    <defaults>
      <allow_any>no</allow_any>
      <allow_inactive>no</allow_inactive>
      <allow_active>auth_admin_keep</allow_active>
      <!-- 这个defaults节点下的所有子节点可以有这些值no,yes,auth_self,auth_admin,auth_self_keep,auth_admin_keep　-->
    </defaults>
    <!-- 权限提升的可执行文件，需是二进制文件 -->
    <annotate key="org.freedesktop.policykit.exec.path">/usr/bin/dde-file-manager</annotate>
    <annotate key="org.freedesktop.policykit.exec.allow_gui">true</annotate>
  </action>
</policyconfig>
```

**配置项：**

**vendor:** action的项目或供应商的名称，可选

**vendor_url:** action的项目或供应商的 URL，可选

**icon_name:** action的项目或供应商的图标，可选

**action:** 声明一个action。action名称使用唯一的id属性指定， 并且只能包含字符，[A-Z][a-z][0-9].- 例如 ASCII、数字、句点和连字符。

可以在action 中使用的元素：

**1. description:** action具体的操作信息描述

**2. message:** 弹窗提示信息

**3. defaults:**

defaults用于权限设置。它包含三个设置：

- allow_any 适用于任何客户端的隐式授权。可选的。
- allow_inactive
适用于本地控制台上非活动会话中的客户端的隐式授权。可选的。
- allow_active 适用于本地控制台上活动会话中的客户端的隐式授权。可选的。

非活动会话通常是远程会话（SSH、VNC 等），而活动会话直接登录到机器上的 TTY 或 X 显示器。

每个设置均可使用以下参数：

- no：用户无权执行该操作。因此不需要认证。
- yes : 用户被授权执行操作而无需任何身份验证。
- auth_self：需要身份验证，但用户不必是管理用户。
- auth_admin：需要以管理用户身份进行身份验证。
- auth_self_keep：与 auth_self 相同，但与 sudo 一样，授权持续几分钟。
- auth_admin_keep：与 auth_admin 相同，但与 sudo 一样，授权持续几分钟。

**4. annotate:** 使用键/值对来配置action注释

- org.freedesktop.policykit.exec.path: 由polkit附带的pkexec程序使用。要提权的可执行文件路径，脚本可用`sh -c test.sh`。
- org.freedesktop.policykit.exec.allow_gui: pkexec是否允许界面程序。
- org.freedesktop.policykit.imply: 可以被用于定义 元动作。
- org.freedesktop.policykit.owner: 可以用来定义一组用户谁可以查询客户机是否被授权执行此操作。如果未指定此注释，则只有 root 可以查询以不同用户身份运行的客户端是否有权执行操作。

**5. vendor:** 供应商名称，可选

**6. vendor_url:** 供应商 URL，可选

**7. icon_name:** 图标名称，可选

### rules

`addRule()`方法用于添加一个函数，该函数可以在执行action 和主题的授权检查时调用。函数按照它们被添加的顺序被调用，直到其中一个函数返回一个值。

`addAdminRule()`方法用于添加在需要管理员身份验证时调用的函数。该函数用于指定哪些身份可用于管理员身份验证，用于由操作和主题标识的授权检查。添加的函数按照它们被添加的顺序调用，直到其中一个函数返回一个值。默认配置包含在`50-default.rules`中。

**例子：**

**禁用挂起和休眠**

以下规则为所有用户禁用挂起和休眠。

```shell
/etc/polkit-1/rules.d/10-disable-suspend.rules
polkit.addRule(function(action, subject) { 
    if (action.id == "org.freedesktop.login1.suspend" || 
        action.id == "org.freedesktop.login1.suspend-multiple-sessions" || 
        action .id == "org.freedesktop.login1.hibernate" || 
        action.id == "org.freedesktop.login1.hibernate-multiple-sessions") 
    { 
        return polkit.Result.NO; 
    } 
});
```

**允许管理员组中的用户无需身份验证即可运行 GParted**

```shell
/* Allow users in admin group to run GParted without authentication */
polkit.addRule(function(action, subject) {
    if (action.id == "org.archlinux.pkexec.gparted" &&
        subject.isInGroup("admin")) {
        return polkit.Result.YES;
    }
});
```

**绕过密码提示**

实现类似于sudo NOPASSWD选项的功能并仅根据用户/组身份获得授权

以 root 身份创建/etc/polkit-1/rules.d/49-nopasswd_limited.rules文件

```shell
/* Allow members of the wheel group to execute the defined actions 
 * without password authentication, similar to "sudo NOPASSWD:"
 */
polkit.addRule(function(action, subject) { 
    if (subject.isInGroup("wheel")) { 
        return polkit.Result.YES; 
    } 
});

/* 针对特定程序 */
polkit.addRule(function(action, subject) { 
    if ((action.id == "org.archlinux .pkexec.gparted" || 
     action.id == "org.libvirt.unix.manage") && subject.isInGroup 
        ("wheel")) 
    { 
        return polkit.Result.YES; 
    } 
});
```

**下面的规则将使 polkit 要求输入 root 密码而不是管理员身份验证的用户密码。**

```shell
/etc/polkit-1/rules.d/49-rootpw_global.rules
/* Always authenticate Admins by prompting for the root
 * password, similar to the rootpw option in sudo
 */
polkit.addAdminRule(function(action, subject) {
    return ["unix-user:root"];
});
```
