# 准备工作

烧录U盘：用Mac的Etcher最快

开机按F12

# 一、硬盘分区与格式化

查看硬盘及标识符：

```bash
lsblk -f		# 确认磁盘标识符
fdisk -l		# 查看磁盘
```

擦除硬盘：两种方式

```bash
# 仅清楚文件系统签名，分区表仍保留，不破坏分区结构，用于快速重置文件系统
wipefs -a /dev/nvme0n1
wipefs -a /dev/sda

# 完全销毁GPT分区表，用于重新初始化磁盘
sgdisk --zap-all /dev/nvme0n1
```

分区方案：

- nvme硬盘：
    - EFI：2GB
    - Root：剩余（btrfs）
- sda硬盘：Home，全部空间（btrfs）

```bash
parted /dev/nvme0n1 mklabel gpt

fdisk /dev/nvme0n1 	# 对磁盘分区
gdisk /dev/nvme0n1  # 使用GPT分区表
gdisk /dev/sda

cgdisk /dev/nvme0n1
```

`fdisk`命令：

```plaintext
'g' 创建新的 GPT 分区表
'n' 创建新分区，分区号1，起始扇区默认，大小 +2G，类型 'EFI System',若类型不对则用`t`命令，输入1改成efi类型
'n' 创建新分区，分区号2，起始扇区默认，大小默认（剩余空间），类型 'Linux filesystem'
'w' 写入并退出
```

`gdisk`命令：

```plaintext
o       # 创建新 GPT 分区表
n       # 新建分区 1
1       # 分区号 1
[Enter] # 默认起始扇区
+2048M   # 大小 
ef00    # 类型为 EFI 系统分区，8300为Linux文件系统
w       # 写入并退出
```

`cgdisk`命令：

```plaintext
n		# 创建新分区，size 2048M，type EF00/8300
w		# 写入分区表
y		# 确认保存
```





> 分区可能看见`remove signature`的提示，输入yes，清楚旧的分区表签名，确保新分区表干净

格式化

```python
# /boot
mkfs.fat -F32 /dev/nvme0n1p1
# /
mkfs.btrfs -f /dev/nvme0n1p2 	# -f或--force，强制创建文件系统，忽略已有的fs
# /home
mkfs.btrfs -f /dev/sda1
```

挂载

```bash
mount /dev/nvme0n1p2 /mnt
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
mount /dev/sda1 /mnt/home
```

/boot：存放Linux内核、初始内存盘(initramfs)、引导加载程序配置文件(GRUB)

# 二、安装基础系统

```bash
pacstrap /mnt base base-devel linux linux-firmware nano git
```

生成fstab

```bash
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

基础配置

```bash
arch-chroot /mnt 	# 临时将根目录切换到/mnt，但未脱离当前内核

# 时区设置（示例为上海）
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc

# 本地化设置
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
echo "zh_CN.UTF-8 UTF-8" >> /etc/locale.gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
locale-gen

# 主机名
echo "archlinux" > /etc/hostname

# 安装必要软件
pacman -S networkmanager sudo grub efibootmgr intel-ucode nano vim
systemctl enable NetworkManager

# 配置引导（GRUB）
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg

# 用户设置
passwd 	# root
useradd -m -G wheel -s /bin/bash username 	# 普通用户
passwd username
EDITOR=vim visudo 	# 取消%wheel ALL=(ALL) ALL的注释

exit
umount -R /mnt
reboot
```

安装后配置

```bash
# kde核心组件，X11显示服务器，登录管理器
sudo pacman -S plasma konsole dolphin xorg-server xorg-xinit sddm
sudo systemctl enable sddm
sudo pacman -S kde-applications-meta
```

## 换源

```bash
sudo pacman -S reflector
sudo reflector --country China --age 12 --sort rate --save /etc/pacman.d/mirrorlist
```



## 中文输入法

```bash
sudo pacman -S kde-l10n-zh_cn fcitx5 fcitx5-chinese-addons fcitx5-qt fcitx5-gtk fcitx5-configtool

vim ~/.pam_environment
# 加入如下内容
# GTK_IM_MODULE=fcitx
# QT_IM_MODULE=fcitx
# XMODIFIERS=@im=fcitx
```

KDE系统设置，区域，输入法，添加fcitx5

切换输入法快捷键



## v2ray，v2raya

```bash
sudo systemctl enable --now v2raya 	# 设置开机自启
```

打开浏览器，访问`https://127.0.0.1:2017`

添加订阅，设置透明代理

## yay

```bash
sudo pacman -Syyu	# 更新系统
sudo pacman -S --needed base-devel git	# 编译工具
mkdir -p ~/aur && cd ~/aur
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

# 其他应用

```bash
yay -S google-chrome
yay -S visual-studio-code-bin	# 推荐版本，别的版本有坑
yay -S typora 1.10.8-1 		# 需指定版本，或-Ss搜索
yay -S wechat-universal-bwrap
```

壁纸管理feh，图像查看器Gwenview，视频播放器mpv，PDF查看器Okular，音乐播放器Elisa，邮件Thunderbird

## pytorch开发环境

cuda版，用miniconda管理

vscode下载python、jupyter扩展

```bash
conda create -n pytorch python=3.11
conda activate pytorch

```

