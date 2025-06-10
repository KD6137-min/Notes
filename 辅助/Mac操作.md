## Mac 远程连接服务器
准备

1. ip, 端口, 账号, 密钥, 防火墙(安全组)
2. ssh user@ip -p 端口

## 修复已损坏软件

sudo xattr -rd com.apple.quarantine /Applications/WebStrom.app
后面为应用程序中拖到终端的路径名
不行的话, 去掉-r再试

## IP相关操作

查IP
ipconfig getifaddr en0
curl ifconfig.me

改ip

vim /etc/hosts
更改master前面的ip

## 删除.DS_Store
find . -name "*.DS_Store" -type f -delete

禁止.DS_store生成, 打开"终端", 执行下面命令, 重启Mac即可生效
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE

如果需要恢复.DS_store生成, 在终端运行下列代码
defaults delete com.apple.desktopservices DSDontWriteNetworkStores

## Macbook 使用技巧

Command + Shift + 3: 截取整个屏幕
Command + Shift + 5: 截取窗口或菜单

Command + Shift + .: 查看隐藏内容

## Xmind缓存地址

  ~/Library/Application Support/XMind/Electron v3/vana

## Zsh配置文件

切换zsh:   chsh -s /bin/zsh
切换bash:  chsh -s /bin/bash

zsh读取配置文件: ~/.zshrc文件
bash读取配置文件: ~/.bash_profile文件

打开 zsh: open .zshrc   (open后面有空格)

## 解压rar文件

终端, cd到所在路径

解压: 
unrar x name.rar

压缩: 
rar a name.rar 
## 隐藏灰色文件
defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder

显示隐藏文件
defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder

