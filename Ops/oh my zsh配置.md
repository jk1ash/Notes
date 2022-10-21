## 卸载

```bash
uninstall_oh_my_zsh
```

## 下载安装

需先安装zsh

```bash
sh -c "$(wget -O- https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
或
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

## 设置zsh为默认的shell

```
chsh -s /bin/zsh
```

## 插件与主题(自用)

### 路径

```
/home/.zshrc
/root/.zshrc
```

### 插件

#### zsh-syntax-highlighting

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

#### zsh-autosuggestions

```bash
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

### 主题

- ys
- zeta
- pygmalion

### 应用立即生效

```bash
source ~/.zshrc
```

## 常见错误

### [arguments:450: _vim_files: function definition file not found _arguments:450]

**解决方法：**

```bash
rm ~/.zcompdump*
```

重启所有终端
