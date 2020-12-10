Manjaro的一站式安装流程（以kde版本为例）

去manjaro官网下载想要安装的版本镜像文件并核对sha256值是否正确

使用rufus写入镜像到一个U盘中（若提示是否以DD模式写入则选择以该模式进入），U盘分区表选择GBP（此选择方法是为了可以实现双系统，若不需要双系统也可以选择传统MBR分区表不以DD模式写入）

重启电脑，进入BIOS中选择从U盘启动

在GURB欢迎界面先选择language选项设置语言为中文，模式为简体中文，设置完后进入driver选项根据自己的设备选择驱动版本（选择free的开源驱动或nofree的闭源驱动）（没有显卡选择free，有显卡选择nofree）

选择boot选项进入临时安装系统

进入系统后先连接网络，连接完毕后点击桌面上的install manjaro linux图标，进入安装程序

根据自己的需求选择语言，时区和区域，键盘布局

进入磁盘分区设置后根据自己的实际情况选择安装方案以下为选择手动分区为例，若选择擦除安装建议勾选创建swap分区，以下是建议分区方案

​     分区表类型：GPT
​     分区1：若单系统：挂载 boot 512MB ext4格式，标记为boot，双系统：额外挂载 boot/efi 300MB，fat32格式,在此处标记为boot,并同时标记为bios-grub，单硬盘双系统不要格式化，双硬盘双系统随意。
​     分区2：挂载 /  推荐30GB以上，ext4格式
​     分区3：挂载var  8到12GB,ext4格式
​     分区4：挂载linuxswap，大小为0到当前设备内存大小*60%之间，（特殊情况下可随意设置）
​     分区5：挂载home，推荐大小不小于30GB（建议将所有剩余空间均分配到此处）ext4格式
​     分区6：非挂载分区，作为普通盘使用，格式随意

确认开始安装

系统初步安装完成，下面开始配置源

打开终端（使用sudo指令终端会要求用户输入当前用户密码）
输入

```
sudo pacman-mirrors -i -c China -m rank
```

选择延时最小的源，输入

```
sudo pacman -Syy
```

输入  

```
sudo nano /etc/pacman.conf 
```

在打开的文件的文末输入

```
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

或者输入

```
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

（以上分别为中国科技大学和清华大学的ArchlinuxCN 镜像如想要选择其他镜像站可以打开以下链接查看https://github.com/archlinuxcn/mirrorlist-repo
随后保存，在终端中输入

```
sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
```

来导入PGP Keys
安装yay和base-devel(用于安装AUR软件仓库中的的软件）：
终端中输入

```
sudo pacman -S base-devel yay
```

配置AUR源（使用清华大学的AUR镜像源）：
终端输入

```
yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save 
yay -P -g  //用于查看当前修改状况
```

终端中输入

```
sudo pacman -Syyu
```

安装中文输入法（以fcitx5为例）：
终端输入：

```
sudo pacman -S fcitx5  fcitx5-chinese-addons fcitx5-gtk  fcitx5-qt  kcm-fcitx5 fcitx5-rime
```

配置ficitx5：
终端输入：

```
loginctl show-session 2 -p Type
```

若显示错误则输入

```
loginctl show-session "$XDG_SESSION_ID" -p Type
```

若终端输出了x11则
执行以下命令：

```
sudo nano ~/.xprofile
```

在文末添加：

```
export GTK_IM_MODULE=fcitx5
export XMODIFIERS=@im=fcitx5
export QT_IM_MODULE=fcitx5
fcitx5 &
```

随后执行以下命令：

```
sudo nano ~/.xinitrc
```

在文末添加（添加在exec $(get_session)之前）：

```
export GTK_IM_MODULE=fcitx5
export XMODIFIERS=@im=fcitx5
export QT_IM_MODULE=fcitx5
```

若终端未输出x11则
执行以下命令：

```
sudo nano ~/.pam_environment
```

在文末添加：

```
GTK_IM_MODULE DEFAULT=fcitx5
QT_IM_MODULE DEFAULT=fcitx5
XMODIFIERS DEFAULT=@im=fcitx5


```

随后终端输入reboot重启系统
安装git（用于从git仓库中编译安装软件或正常的代码维护）
终端中执行

```
sudo pacman -S git
```

安装debtap（使arch分支的系统可以安装debian分支的软件包）
终端中执行：

```
yay -S debtap
```

国内升级debtap提速（亲测部分网络环境下可能没有用，反而可能会减速）：
终端中执行：

```
sudo nano /usr/bin/debtap
替换
http://ftp.debian.org/debian/dists
为
https://mirrors.ustc.edu.cn/debian/dists
替换
http://archive.ubuntu.com/ubuntu/dists
为
https://mirrors.ustc.edu.cn/ubuntu/dists/
```

退出保存后执行sudo debtap -u（这可能会花费很长的时间，我用了1个半小时）
安装wps（金山的办公三件套）
终端中执行

```
sudo pacman -S wps-office ttf-wps-fonts
```

（如果无法顺利执行可以将sudo pacman改为yay再尝试一次）
可获得一个英文界面的wps
汉化：安装命令：

```
sudo pacman -S wps-office-mui-zh-cn
```

若无法安装则执行：

```
yay -S wps-office-mui-zh-cn
```



选择一个aur源的版本安装 然后重启wps就好了
---------------------------------------------------------------------------------------

配置wine（新手不推荐版，新手推荐版请看下面）（使系统拥有运行部分windows桌面应用程序的能力）
Wine主体：

```
sudo pacman -S wine
```

终端执行

```
WINEARCH=win32 WINEPREFIX=~/.win32 winecfg
```

容器调教
创建wine32环境的容器

```
WINEARCH=win32 WINEPREFIX=~/.win32 winecfg
```

创建wine64环境的容器

```
WINEARCH=win64 WINEPREFIX=~/.win64 winecfg
```

注意：创建过程中会提醒你是否安装wine_gecko和 wine-mono，此处点击取消（稍后手动安装）
附属组件安装（Internet Explorer和.NET两个核心组件）：
Wine版本安装版本为

```
sudo pacman -S wine_gecko wine-mono
```

Windows原版安装为（较麻烦且易失败，但在某些场合下会很有用）
方法1.用户自行到微软官网下载安装，安装指令为

```
msiexec -i 
```

方法2.终端输入以下命令

```
sudo pacman -S winetricks-zh
```

添加wine的默认启动值（设置为以32位容器默认启动）
终端输入

```
sudo nano ~/.xprofile
```

文末输入

```
export WINEARCH=win32
export WINEPREFIX=~/home/stvsl/.win32/
```

完成后保存
终端输入

```
WINEARCH=win32 WINEPREFIX=~/.win32 winetricks
```

选择默认容器，选择添加windows组件或程序进行安装

新手版配置wine：
使用yay安装

```
yay -S q4wine # 一个图形界面的wine
yay -S crossover # wine的商业版
```



# deepin 系的软件

```
sudo pacman -S deepin-picker # 深度取色器
sudo pacman -S deepin-screen-recorder # 录屏软件，可以录制 Gif 或者 MP4 格式
sudo pacman -S deepin-screenshot # 深度截图
sudo pacman -S deepin-system-monitor # 系统状态监控
yay -s deepin-wine-wechat # 微信
yay -S deepin-wine-tim # QQ
```


# 开发软件

```
sudo pacman -S jdk11-openjdk
sudo pacman -S make
sudo pacman -S cmake
sudo pacman -S clang
sudo pacman -S nodejs
sudo pacman -S npm
sudo pacman -S goland
sudo pacman -S vim
sudo pacman -S maven
sudo pacman -S pycharm-professional # Python IDE
sudo pacman -S intellij-idea-ultimate-edition # JAVA IDE
sudo pacman -S goland # Go IDE
sudo pacman -S visual-studio-code-bin # 宇宙第一IDE vscode
sudo pacman -S qtcreator # 一款QT开发软件
sudo pacman -S postman-bin
sudo pacman -S insomnia # REST模拟工具
sudo pacman -S gitkraken # GIT管理工具
sudo pacman -S wireshark-qt # 抓包
sudo pacman -S zeal
sudo pacman -S gitkraken # Git 管理工具
```


# 办公软件

```
sudo pacman -S google-chrome
sudo pacman -S foxitreader # pdf 阅读
sudo pacman -S bookworm # 电子书阅读
sudo pacman -S unrar unzip p7zip
sudo pacman -S goldendict # 翻译、取词
yay -S typora # markdown 编辑
yay -S electron-ssr 
yay -S xmind
```


# 设计

```
sudo pacman -S pencil # 免费开源界面原型图绘制工具
yay -S mockingbot # 一款付费原型绘制软件
```


# 娱乐软件

```
sudo pacman -S netease-cloud-music # 网易云音乐
yay -S qqmusic-bin # qq音乐 
```


# 下载软件

```
sudo pacman -S aria2
sudo pacman -S filezilla  # FTP/SFTP
sudo pacman -S freedownloadmanajer
```


# 图形

```
sudo pacman -S gimp # 修图
sudo pacman -S kdenlive # 视频剪辑
sudo pacman -S lmms # 编曲
```


# 系统工具

```
sudo pacman -S albert #类似Mac Spotlight，另外一款https://cerebroapp.com/
yay -S copyq #  剪贴板工具，类似 Windows 上的 Ditto
sudo pacman -S timeshift # 系统备份工具，防止因意外造成的系统崩溃而丢失数据
```


# 终端

```
sudo pacman -S screenfetch # 终端打印出你的系统信息，screenfetch -A 'Arch Linux'
sudo pacman -S htop
sudo pacman -S bat
sudo pacman -S yakuake # 堪称 KDE 下的终端神器，KDE 已经自带，F12 可以唤醒
sudo pacman -S net-tools # 这样可以使用 ifconfig 和 netstat
yay -S tldr
yay -S tig # 命令行下的 git 历史查看工具
yay -S tree
yay -S ncdu # 命令行下的磁盘分析器，支持Vim操作
yay -S mosh # 一款速度更快的 ssh 工具，网络不稳定时使用有奇效
```


必备字体安装

```
 sudo pacman -S wqy-bitmapfont wqy-microhei \
  wqy-zenhei adobe-source-code-pro-fonts \
  adobe-source-sans-pro-fonts adobe-source-serif-pro-fonts \
  adobe-source-han-sans-cn-fonts ttf-monaco ttf-dejavu ttf-hanazono \
  noto-fonts noto-fonts-cjk noto-fonts-emoji 
```

