# ArchLinux Notes
記錄日常配置與使用 ArchLinux
### 配置使用 zsh
1. 安裝 zsh
```
$ sudo pacman -S zsh
```
2. 添加用戶並使用 `-s` 標記默認 shell，或者使用 `usermod` 修改現有用戶的默認 shell
```
$ sudo usermod -s /usr/bin/zsh <USERNAME>
```
3. 可以使用 [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh) 一鍵配置
```
$ curl -sSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh | bash
```
編輯 `~/.zshrc` 配置插件主題等，詳細參考 [Oh My Zsh wiki](https://github.com/ohmyzsh/ohmyzsh/wiki)

4. 仿 Fish 命令高亮：你可以从官方仓库里安装 zsh-syntax-highlighting，然后添加下面的配置到你的 zshrc 中
```
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

# matches brackets and parenthesis.
ZSH_HIGHLIGHT_HIGHLIGHTERS=(main brackets)

# Declare the variable
typeset -A ZSH_HIGHLIGHT_STYLES

# To have single-hyphen-option and double-hyphen-option colored
ZSH_HIGHLIGHT_STYLES[single-hyphen-option]='fg=cyan'
ZSH_HIGHLIGHT_STYLES[double-hyphen-option]='fg=cyan'
```



### 參考文檔
+ [Oh My Zsh wiki](https://github.com/ohmyzsh/ohmyzsh/wiki)
+ [docs on highlighters](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/docs/highlighters.md)
