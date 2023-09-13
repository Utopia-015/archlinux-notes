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
3. 安装 zsh 插件管理器 [zinit](https://github.com/zdharma-continuum/zinit)
```
$ sh -c "$(curl -fsSL https://git.io/zinit-install)"
```
編輯 `~/.zshrc` 配置插件主題等，詳細參考 [Zinit Introduction](https://zdharma-continuum.github.io/zinit/wiki/INTRODUCTION/)
```
# 使用 p10k 主题，需要安装 ttf-meslo-nerd-font-powerlevel10k(AUR)
zinit ice depth=1
zinit light romkatv/powerlevel10k
```
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

### 使用 PipeWire
1. Session 会话管理选择 WirePlumber
2. 安装 pipewire-pulse, 如提示与 pulseaudio 和 pulseaudio-bluetooth 冲突则确认移除。重新启动、重新登录或 start `pipewire-pulse.service` user unit 以查看效果。
   通常，不需要进一步的操作，应用应作为用户服务自动启用 `pipewire-pulse.socket`。要检查替换是否正常工作，请运行以下命令并查看输出：
   ```
   $ pactl info
   ...
   Server Name: PulseAudio (on PipeWire 0.3.79)
   ...
   ```

### Smb 文件共享
1. 安裝 Samba
```
$ sudo pacman -S samba
```
2. Samba 的配置文件是 `/etc/samba/smb.conf`，[smb.conf(5)](https://man.archlinux.org/man/smb.conf.5) 提供了詳細文檔，
samba 包默認沒有提供該文件，可以下載本倉庫的 [smb.conf](smb.conf) 使用
3. Samba 需要 Linux 賬戶才能使用，用戶名可以和 Linux 共享，但其使用單獨的密碼管理
```
$ sudo smbpasswd -a <samba_user>
```
4. start/enable `smb.service`, 建議將使用服務的用戶添加進 `wheel` 用戶組，以免 journal 出現問題

### 網絡配置
如果對網絡接口的配置沒有特殊要求，可直接使用 NetworkManager 或 dhcpcd，
但是如果需要配置較爲複雜的接口，比如網橋或 tap 接口，可使用 `systemd-networkd.service`，
其配置文件位於 `/usr/lib/systemd/network`, `/usr/local/lib/systemd/network`, `/run/systemd/network` 和 `/etc/systemd/network`，
/etc/ 中的文件具有最高優先級，/run/ 比 /usr/ 有着更高優先級，同名的文件會被更高優先級的替換。
創建虛擬接口使用 `.netdev` 擴展名的文件，接口配置使用 `.network` 擴展名的文件，注意此處對擴展名有嚴格要求，
詳細請參考 [systemd.network(5)](https://man.archlinux.org/man/systemd.network.5.en) 以及 
[systemd.netdev(5)](https://man.archlinux.org/man/systemd.netdev.5.en)

Example: 定義一個網橋和一個 tap 接口，將以太網口和 tap 橋接，網橋使用 DHCP 獲取 IP
```
# /etc/systemd/network/20-bridge.netdev
[NetDev]
Description=Create a bridge named br0
Name=br0
Kind=bridge

# /etc/systemd/network/20-bridge.network
[Match]
Name=br0
[Link]
ActivationPolicy=always-up
[Network]
DHCP=yes
# Address=192.168.0.15/24
# Gateway=192.168.0.1

# /etc/systemd/network/25-enp.network
[Match]
Name=en*
[Link]
ActivationPolicy=always-up
ARP=false
[Network]
Bridge=br0

# /etc/systemd/network/30-tap.netdev
[NetDev]
Description=Create a tap named tap0
Name=tap0
Kind=tap

# /etc/systemd/network/30-tap.network
[Match]
Name=tap0
[Network]
Bridge=br0
[Bridge]
Cost=4
```




### 參考文檔
+ [Zinit Introduction](https://zdharma-continuum.github.io/zinit/wiki/INTRODUCTION/)
+ [docs on highlighters](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/docs/highlighters.md)
+ [ArchWiki#Samba](https://wiki.archlinux.org/title/Samba)
+ [systemd.network(5)](https://man.archlinux.org/man/systemd.network.5.en)
+ [systemd.netdev(5)](https://man.archlinux.org/man/systemd.netdev.5.en)
+ [PipeWire#用法](https://wiki.archlinuxcn.org/wiki/PipeWire#%E7%94%A8%E6%B3%95)
