---
title: Debian
date: 2019-08-01
categories:
  - linux
tags:
  - linux
  - debian
layout: post
---

**注意**：以各类官方文档为准，本文件部分内容可能**多年未更新**。

```sh
/etc/motd

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
```

## 开源精神与自由理想

[`Linus Torvalds <torvalds@linux-foundation.org>`](https://lore.kernel.org/all/CAHk-=whNGNVnYHHSXUAsWds_MoZ-iEgRMQMxZZ0z-jY4uHT+Gg@mail.gmail.com/)

```sh
From: Linus Torvalds <torvalds@linux-foundation.org>
To: Tor Vic <torvic9@mailbox.org>
Cc: Kexy Biscuit <kexybiscuit@aosc.io>,
	jeffbai@aosc.io, gregkh@linuxfoundation.org,
	 wangyuli@uniontech.com, aospan@netup.ru,
	conor.dooley@microchip.com,  ddrokosov@sberdevices.ru,
	dmaengine@vger.kernel.org, dushistov@mail.ru,
	 fancer.lancer@gmail.com, geert@linux-m68k.org,
	hoan@os.amperecomputing.com,  ink@jurassic.park.msu.ru,
	linux-alpha@vger.kernel.org,
	 linux-arm-kernel@lists.infradead.org,
	linux-fpga@vger.kernel.org,  linux-gpio@vger.kernel.org,
	linux-hwmon@vger.kernel.org,  linux-ide@vger.kernel.org,
	linux-iio@vger.kernel.org,  linux-media@vger.kernel.org,
	linux-mips@vger.kernel.org,  linux-renesas-soc@vger.kernel.org,
	linux-spi@vger.kernel.org,  manivannan.sadhasivam@linaro.org,
	mattst88@gmail.com, netdev@vger.kernel.org,  nikita@trvn.ru,
	ntb@lists.linux.dev, patches@lists.linux.dev,
	 richard.henderson@linaro.org, s.shtylyov@omp.ru, serjk@netup.ru,
	 shc_work@mail.ru, tsbogend@alpha.franken.de,
	v.georgiev@metrotek.ru,  wsa+renesas@sang-engineering.com,
	xeb@mail.ru
Subject: Re: [PATCH] Revert "MAINTAINERS: Remove some entries due to various compliance requirements."
Date: Wed, 23 Oct 2024 10:45:47 -0700	[thread overview]
Message-ID: <CAHk-=whNGNVnYHHSXUAsWds_MoZ-iEgRMQMxZZ0z-jY4uHT+Gg@mail.gmail.com> (raw)
In-Reply-To: <124c1b03-24c9-4f19-99a9-6eb2241406c2@mailbox.org>

Ok, lots of Russian trolls out and about.

It's entirely clear why the change was done, it's not getting
reverted, and using multiple random anonymous accounts to try to
"grass root" it by Russian troll factories isn't going to change
anything.

And FYI for the actual innocent bystanders who aren't troll farm
accounts - the "various compliance requirements" are not just a US
thing.

If you haven't heard of Russian sanctions yet, you should try to read
the news some day.  And by "news", I don't mean Russian
state-sponsored spam.

As to sending me a revert patch - please use whatever mush you call
brains. I'm Finnish. Did you think I'd be *supporting* Russian
aggression? Apparently it's not just lack of real news, it's lack of
history knowledge too.

                      Linus
```

## RTFM

请系统性阅读手册和维基。

- [`Debian`用户手册](https://www.debian.org/doc/user-manuals)
- [`Debian`开发者手册](https://www.debian.org/doc/devel-manuals)

- [`Debian Wiki`](https://wiki.debian.org)
- [`Arch Wiki`](https://wiki.archlinux.org)

- [`Debian`打包门户](https://wiki.debian.org/Packaging)
- [`Arch`软件包指导原则](https://wiki.archlinux.org/title/Arch_package_guidelines)

## 安装系统

### ISO

- 如果使用`stable`，从镜像站的`debian-cd`中选择
- 如果使用`testing`，从镜像站的`debian-cdimage/weekly-builds`中选择

`Debian`官方**不提供**`debian unstable/sid`的ISO，只可以从`stable`或`testing`升级到`unstable/sid`

部分镜像站**不提供**`debian-cdimage`，`testing`的`weekly-builds`安装镜像是自动构建的，安装过程中可能出现问题，建议使用`stable`镜像最小化安装然后升级到`testing`或`unstable/sid`

### 制作启动U盘

**警告**：系统语言应当设置为英文，防止tty乱码，桌面语言可以在桌面设置内更改。

```sh
# 制作启动U盘
lsblk # sudo fdisk -l
sudo umount /dev/sd<x>[1]
sudo dd if=debian.iso of=/dev/sd<x> bs=1M
sudo eject /dev/sd<x>
```

### 分区

- `EFI` 1~2G
- `/`
- `swap` 默认1~2G
- 酌情单独分区 `/home` `/var` `/tmp`
- 数据分区酌情设置挂载选项 `nodev,noexec,nosuid`

### sources.list & preferences

**警告**：**不建议release混用**，如果需要特定release内的软件（如`sid/firefox` `sid/virtualbox`），请**提前**配置好合理的`/etc/apt/preferences`！之后再启用`testing/unstable`，详情参考`man 5 apt_preferences`

```sh
# 如果没有网络连接，挂载本地ISO
mkdir -p /media/iso
mount -o loop debian-DVD.iso /media/iso/
nano /etc/apt/sources.list
# deb [trusted=yes] file:///media/iso/ stable main contrib
```

## 基础程序

```sh
# debootstrap required important
tasksel install standard

# login as root
apt update                  # apt -o Acquire::ForceIPv4=true update
apt upgrade                 # apt -o Acquire::ForceIPv4=true upgrade
apt install                 ssh vim tmux git firewalld needrestart \
                            # debian-reference debian-handbook \
                            # apt-listbugs (for testing/sid) \
                            # apt-transport-https (debian10之后不再需要手动安装)
adduser username sudo # 添加用户到某个属组，需要重新登入生效，详情参考`man adduser`, `man useradd`, `id -nG`
reboot
```

**注意**：记得开启并配置防火墙，开放必要的端口或服务。

**注意**：`apt-listbugs`和`unattended-upgrades`一起使用时，`apt-listbugs`如果下载bug信息失败，`unattended-upgrades`会一直请求下载然后一直失败。不要一起使用。

### Kernel

```sh
# hold kernel
sudo dpkg --get-selections | grep linux
sudo apt-mark hold linux-image-x.x.x-x
```

```sh
# build kernel
sudo apt install            build-essential libncurses-dev fakeroot \
                            linux-source \
                            linux-image-amd64 \
                            linux-headers-amd64

cd /path/to/kernel/source
#cp -v /boot/config-$(uname -r) .config
make menuconfig
make [deb-pkg] -j[8]
```

### Firmware

```sh
# 查看硬件信息
sudo lspci -k | grep -E "(3D|Audio|Ethernet|Network|VGA|Wireless)"
sudo apt install            firmware-linux \
                            # firmware-iwlwifi firmware-realtek \
                            # firmware-sof-signed \
                            # bluez

# 笔记本
sudo apt install task-laptop
sudo tasksel install laptop

# 监控硬件信息（温度，SSD-S.M.A.R.T）
sudo apt install hw-probe # 安装hw-probe会根据依赖安装检测硬件的各种工具，如lm-sensors

# 自动推荐固件（自动通知有点烦人）
sudo apt install isenkram
sudo isenkram-lookup

sudo reboot
```

**注意**：在nvidia独显笔记本上安装`nvidia`闭源驱动仍然会不可预测地`kernel panic`（`capslock`闪烁），建议在选择设备时慎重考虑。

NVIDIA驱动与桌面环境混成器需要支持[显式同步(explict sync)](https://zamundaaa.github.io/wayland/2024/04/05/explicit-sync.html)，否则Wayland下X11应用程序可能会闪烁

- nvidia-drvier >= 555
- gnome >= 46.1
- kde plasma >= 6.1

```sh
# 为了减少驱动安装失败GUI无法正常使用的影响，可以临时设置系统启动到tty(`multi-user.target`)
sudo systemctl get-default
sudo systemctl set-default multi-user.target

sudo apt install dkms linux-headers-amd64
sudo apt install firmware-linux firmware-misc-nonfree
sudo apt install nvidia-driver
sudo /sbin/modinfo -F version nvidia-current

# 从`debian11`开始`apt install nvidia-driver`安装完成之后已经不再需要配置，但是需要在`bios`里关闭安全启动或自签名内核

# 使用wayland可能需要额外配置，详情参考`debian nvidia wayland` `gnome nvidia wayland` `kde nvidia wayland`
echo 'GRUB_CMDLINE_LINUX="$GRUB_CMDLINE_LINUX nvidia-drm.modeset=1"' | sudo tee /etc/default/grub.d/nvidia-modeset.cfg
sudo update-grub

sudo systemctl enable nvidia-suspend.service nvidia-hibernate.service nvidia-resume.service

sudo -e /etc/modprobe.d/nvidia-power-management.conf
# options nvidia NVreg_PreserveVideoMemoryAllocations=1

sudo reboot

# 检查是否启用成功
sudo cat /sys/module/nvidia_drm/parameters/modeset # Y
sudo cat /proc/driver/nvidia/params | grep Preserve # PreserveVideoMemoryAllocations: 1

# 检查GUI是否正常
sudo systemctl start [gdm/sddm]
# gdm如果无wayland选项，可以禁用gdm检查规则
sudo ln -sv /dev/null /etc/udev/rules.d/61-gdm.rules

# GUI正常则设置系统启动到图形界面
sudo systemctl set-default graphical.target

# 增加架构 steam
sudo dpkg --add-architecture i386
sudo apt install nvidia-driver-libs:i386
```

其他驱动参考`debian wiki firmware`及`debian wiki +硬件名`

- 从`Debian12`开始安装系统时会检测并安装非自由固件，基本不再需要手动安装，如仍有无法驱动的硬件请手动检查`dmesg`并搜索阅读官方wiki
- `wacom`手写板、`xbox`手柄等内核自带驱动，不需要手动安装驱动即可使用
- 一些特殊硬件能驱动的可能性不大，比如某些声卡，指纹等

安装完成使用`sudo dmesg`查看内核启动信息，确认驱动正常

- `BIOS`可能会有`ERROR`，`iwlwifi`可能会有`ERROR`，不影响使用的`ERROR`不用管
- `Intel i915 driver`包含了所有支持的iGPU，但是`update-initramfs`并不能检查你需要哪些firmware，所以会有一些如`W: Possible missing firmware /lib/firmware/i915/rkl_xxx_xxxx_xx.bin for module i915`的警告，可以忽略

## 图形界面

### GNOME

[`debian wiki gnome`](https://wiki.debian.org/Gnome)

```sh
# 最小化安装Gnome
sudo apt install [-R]       gnome-core \
                            fonts-noto-cjk fonts-firacode \
                            ibus-libpinyin ibus-table-wubi \
                            # file-roller p7zip-full \
                            # gnome-shell-extension-appindicator \
                            # gnome-shell-extension-dash-to-panel

# 安装完整的Gnome
sudo apt install [-R]   gnome

# 在gdm界面隐藏用户
# /var/lib/AccountsService/users/username  # SystemAccount=true

# 删除gnome-core中不需要的程序（这会打破gnome-core的依赖）
sudo apt-get purge gnome-software gnome-contacts
sudo apt autoremove
```

- 中文输入法
  - 在`gnome-setting`的键盘->输入源中添加中文（智能拼音）
  - `ibus-libpinyin`可以提供中文拼音输入，词库和词语联想不太丰富，胜在简单，不需要配置
- 中文字体
  - 设置桌面语言为中文且安装`Noto Sans CJK`后等宽字体`Monospace`会缺省到`Noto Sans Mono CJK`，某些应用可能会因为该字体太狭长的问题导致排版异常，详见`dotfiles/shell/fontconfig`

- 如果深受桌面环境的各种`bug/feature`的折磨，那么欢迎来到`GNU/Linux`的桌面世界。

### KDE

[`debian wiki kde`](https://wiki.debian.org/KDE)

```sh
# 最小化安装KDE
sudo apt install [-R]       kde-plasma-desktop plasma-workspace-wayland
# 安装完整的KDE
sudo apt install [-R]       kde-standard \
                            fcitx5 fcitx5-chinese-addons \
                            # kde-full
```

## 常用程序

**注意**：**有些工具已经过时或包名已经变更**

### 终端工具

```sh
sudo apt install            fzf fd-find     # find fdfind
                            p7zip-full      # tar zip
                            ranger          # vi style file manager
                            ripgrep         # grep rg
                            tldr            # man
                            htop btop       # top
```

### 网络

```sh
sudo apt install            netcat-openbsd proxychains-ng
```

### 开发

```sh
sudo apt install            shellcheck \
                            clang clang-format clang-tidy clang-tools \
                            clangd \
                            lld lldb llvm llvm-dev \
                            cmake cmake-format \
                            ccache \
                            # valgrind \
                            # cppcheck \
                            # cppman cppreference-doc-en-html \
                            # libboost-all-dev \
                            # qtcreator
sudo apt install            python3-doc pip
sudo apt install            # default-jdk default-jdk-doc
sudo apt install            sqlite3 sqlitebrowser
sudo apt install            # zeal devhelp

# set python3 (debian11之后已默认python3)
#sudo update-alternatives --list python
#sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1
python --version
# or
#echo "alias python='python3'" >> ~/.bashrc
```

### Vim

```sh
# sudo apt purge vim-tiny
sudo apt install vim # vim-nox

# 使用`sudo -e/sudoedit`编辑需要root权限的文件

# 用户vimrc
vi ~/.vimrc

# 系统vimrc
sudo cp /dotfiles/pathto/vimrc /etc/vim/vimrc.local

# neovim
# 从apt源内安装，版本较落后
sudo apt install neovim
# 或者从github下载`nvim-linux64.deb`或Appimage
sudo apt install ./nvim-linux64.deb
# nvim使用`~/.config/nvim/init.vim`，vim使用`~/.vimrc`
touch ~/.vimrc
```

### 源码编译

从源码编译程序，以emacs为例

```sh
# build emacs
#sudo apt build-dep emacs # 不推荐，很难删除依赖
# 使用 mk-build-deps
sudo apt install devscripts
sudo mk-build-deps emacs
sudo apt install ./emacs-build-deps*.deb # build完成后apt purge删除该包
# 获取emacs源码
git clone git://git.savannah.gnu.org/emacs.git
cd emacs
git branch -a
git checkout emacs-28
./autogen.sh
#sudo apt install libgccjit-*-dev
./configure --with-native-compilation
#sudo apt install libgtk-*-dev libwebkit2gtk-*-
#./configure --with-cairo --with-xwidgets --with-x-toolkit=gtk3
make -j$(nproc)
sudo make install
```

### 文档媒体

```sh
sudo apt install            calibre pandoc \
                            texlive-xetex texlive-lang-cjk \
                            texlive-science \
                            # vlc gimp krita blender shotcut etc...
```

### 更多

```sh
sudo apt install fortunes-zh cowsay oneko gtypist
fortune-zh | cowsay -f dragon-and-cow
sudo reboot # or unplug the power =-=
```

## 配置

### Grub引导

```sh
sudo -e /etc/default/grub
sudo update-grub

#GRUB_TIMEOUT=2
#GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet" # splash
#GRUB_DISABLE_OS_PROBER=false
#GRUB_TERMINAL=console

# kernel loglevel
# 0     emerg
# 1     alert
# 2     crit
# 3     err
# 4     warn
# 5     notice
# 6     info
# 7     debug
# sudo dmesg -l warn,err

# grub theme
# /etc/grub.d/05_debian_theme
```

### 区域和语言

/etc/default/locale

- `LANG` 默认语言
  - 所有未显式设置的`LC_*`变量会使用`LANG`的参数
  - `C.UTF-8`: Computer bytes/ASCII
- `LANGUAGE` 缺省语言（桌面环境，图形界面等）
  - 仅当`LC_ALL`和`LANG`没有被设置为`C.UTF-8`时生效
- `LC_ALL` 格式（时间，单位等）
  - `LC_ALL=C.UTF-8`覆盖所有的`LC_*`设置

- 使用`sudo dpkg-reconfigure locales`配置系统locale
- 使用`/etc/locale.gen`和`locale-gen`和`/etc/default/locale`配置系统locale
- 使用`LC_ALL=* LANG=* LANGUAGE=* locale`在shell中测试
- 使用`$XDG_CONFIG_HOME/locale.conf`配置用户locale
- 图形界面语言在桌面设置中设置

```sh
# /etc/default/locale
LANG=C.UTF-8
# ~/.config/locale.conf
LANG=zh_CN.UTF-8
LANGUAGE=zh_CN.UTF-8:en_US.UTF-8

# 如果使用桌面环境，可以只在/etc/default/locale中设置`LANG=C.UTF-8`或`LANG=en_US.UTF-8`，不设置LANGUAGE
# 在桌面的`设置``区域与语言`中选择语言
```

### 无人值守更新

无人值守更新尽量**不要设置删除旧包或安装新包**

[debian wiki unattended upgrade](https://wiki.debian.org/UnattendedUpgrades)

```sh
sudo apt install unattended-upgrade
# /etc/apt/apt.conf.d/50unattended-upgrades
# /etc/apt/apt.conf.d/20auto-upgrades
sudo dpkg-reconfigure unattended-upgrade
sudo unattended-upgrade -d --dry-run # debug unattended-upgrade

# download
sudo -e /lib/systemd/system/apt-daily.timer.d/override.conf
# upgrade
sudo -e /lib/systemd/system/apt-daily-upgrade.timer.d/override.conf
```

### 虚拟机

```sh
# Virt Manager GUI (libvirt, virtinst)
sudo apt install virt-manager virt-viewer virtinst

# commandline utils, no graphical packages (libvirt, virtinst)
sudo apt install -R libvirt-daemon-system qemu-system qemu-utils virtinst bridge-utils

# add user to libvirt group
sudo usermod -aG libvirt username
#sudo adduser username libvirt

# cockpit-machines (libvirt, virtinst)
sudo apt install cockpit cockpit-machines virtinst

# or virtualbox (disable secure boot)
sudo apt install virtualbox # (unstable contrib or stable fasttrack)
sudo usermod -aG vboxsf username
#sudo adduser username vboxsf
```

## 日常使用的好习惯

[Arch Wiki Security](https://wiki.archlinux.org/title/Security)

在日常使用Linux作为个人桌面或个人服务器时应当遵守的一些好习惯，可以减少问题，避免麻烦，增强系统可靠性。

**选择适配Linux的硬件比选择发行版和软件版本更重要**，硬件的不适配可能会造成很多个人无法解决的问题

使用**系统**应当遵守这些原则

- **最小权限原则**，**不要使用root用户**，**不要滥用权限**，使用`sudo`或`su`，并在完成时退出
  - 应当禁用root用户的ssh登入
  - 不要为了方便给予过多权限，不要滥用sudo权限
  - 错误做法：`sudo chmod 777 -Rc /` `sudo ./script`
  - 正确做法：`chmod 600 filename` `./script`
- **单独分区** `/` `/home` `/var` `/tmp` `/data`
  - 根目录，家目录，变量目录，临时目录，数据目录应当单独分区，个人电脑可以酌情仅家目录单独分区
  - 应当限制存放数据的分区的挂载选项，如`/data nodev nosuid noexec`
  - 个人数据应当存放在数据分区或家目录分区
  - 服务器负载数据应当存放在数据分区，专用的数据目录如`/srv` `/data`
- **备份** `snapshot` `rsync` `tar` `timeshif` ...
  - 定期自动或手动备份系统和数据
  - 数据文件应当单独存储并单独备份
  - 错误做法：将所有数据放在和系统的同一分区，并和系统一起备份
  - 正确做法：将家目录、数据单独分区并定期单独备份
- **启用防火墙** `nftables` `iptables` `firewalld`
  - 应当在系统配置之初启用防火墙
  - 需要通过防火墙的程序和端口按需放行
- 重要系统或程序应当有**备用方案**
  - 错误做法：我是极客，追求最新的程序，最轻量化的安装，所以我只使用某个系统或某个桌面
  - 正确做法：准备两台电脑或两个系统，确保至少一个系统稳定运行，当其中一个因为更新损坏时可以快速切换到另一个继续工作
- 危险操作应当**先实验后实施**
  - 错误做法：边看官方手册或`wiki`，边在生产环境操作硬盘分区
  - 正确做法：先在虚拟机中实验分区操作，然后在物理机实施

使用**包管理器**应当遵守这些原则

- **不要混用release**，除非你清楚的知道会发生什么，并能控制
- **更新前备份**，小更新前数据备份，大更新前系统备份
- 每次更新或安装程序时，应当**先更新软件包索引**`update`，后更新或安装软件包`upgrade/install`，否则容易破坏依赖
  - 错误做法：`sudo apt install appname` `sudo pacman -Syu appname`
  - 正确做法：`sudo apt update && sudo apt upgrade && sudo apt install appname`
  - 正确做法：`sudo pacman -Syu && sudo pacman -S appname`
- 使用`apt-listchanges`在安装前确定程序更新中的改变
- 使用`apt-listbugs`在安装前确定程序更新中的已知bug，对严重bug停止更新

使用**个人程序**应当遵守这些原则

- 个人程序安装应当**不影响和改变系统**
  - 错误做法：升级或降级系统库来满足某个程序的依赖
  - 正确做法：破坏系统依赖的程序在虚拟机或容器内运行
- 个人程序依赖应当**存放在用户空间**
  - 错误做法：用系统的包管理器安装大量`rust` `python` `ruby`等语言库，`vim` `emacs`等插件，等等
  - 正确做法：使用语言或工具自己的包管理器，他们存储在用户空间，更新更快，对系统影响更少。仅在他们存在缺陷或少量需要时才使用系统的包管理器
- 个人程序配置应当**遵循系统规则和用户规则** `man file-hierarchy` `man systemd` `man fonts-conf`, ...
  - 错误做法：下载firefox包，解压到桌面，创建执行bin软链到桌面
  - 正确做法：将firefox包放在系统`/usr/local/share/firefox/`或`/opt/firefox/`下，并软链bin到系统`/usr/local/bin/`下，软链`firefox.desktop`到系统`/usr/share/applications/`下
  - 正确做法：将firefox包放在用户`~/.local/firefox/`下，并软链bin到用户`~/.local/bin/`下，软链`firefox.desktop`到用户`~/.local/share/applications/`下

## 更新日志

### 2019.08

- 汇总`debian`安装和使用笔记

### 2022.04

- 尝试使用脚本快速配置`debian`系统，本文件可能未同步更新

### 2022.10

- 分离配置内容到`dotfiles`的各部分`READEME.md`，本文件可能未同步更新

### 2023.02

- 添加`debian12` `non-free-firmware`相关信息

### 2023.05

- 添加本文件作为`dotfiles`中`debian`部分的`README.md`

### 2024.11

- 添加Linus对删除俄罗斯贡献者名单的回复

## 写在最后

这是我从大学开始用`debian`到现在攒出来的一份笔记，杂七杂八。
那时候花了很多时间面对着`tty`的黑框框，有点儿后悔没有多参加算法比赛，多锻炼编程思维，之后确实付出了很多代价补课。
当然，无论怎么度过这些时间都会后悔的，或者说，只要当时不后悔，后来的后悔都不算数。

