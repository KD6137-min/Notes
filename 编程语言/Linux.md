# Linux

# 一、概述

通用操作系统, 不与特定的硬件绑定, 用C语言编写, 可移植性强, 有内核编程接口, 支持多用户和多任务, 支持安全的分层文件系统, 

## shell

壳程序

- /bin/sh: 已被bash取代

- /bin/bash: Linux默认shell

- /bin/ksh: 兼容于bash

- /bin/tcsh: 整合C Shell, 提供更多功能

- /bin/csh: 已被tcsh取代

- /bin/zsh: 基于ksh, 功能强大

## 快捷键

- Tab自动补全: 按两下获取命令清单
- ctrl+c: 强制终止
- ctrl+d: 产生输入结束符EOF
- ctrl+z: 将进程暂停并放到后台
- ctrl+l: 清屏, 光标移动到第一行
- ⬆️⬇️: 显示历史命令
- 删除

  - ctrl+u: 删除光标以前的输入
  - ctrl+k: 删除光标以后的输入
  - ctrl+w: 删除最后一个词, 以空格为分隔符
- 移动光标

  - ctrl+a: 移动光标到行首
  - ctrl+e: 移动光标到行末

## 目录结构

- /bin: binary, 存放最经常使用的命令
- /boot: 存放启动linux使用的核心文件
- /dev: device, 存放外部设备
- /etc: 存放所有的系统管理的配置文件
- /home: 用户主目录
- /lib: 存放系统最基本的动态连接共享库
- /lost+found: 非法关机时存放文件
- /media: 识别的U盘、光驱等
- /mnt: 使用户临时挂载别的文件系统
- /opt: 安装软件的目录
- /proc: 内核和进程信息
- /root: 超级管理员用户主目录
- /run: 存放系统运行时需要的东西
- /sbin: 超级用户的二进制文件
- /sys: 设备的伪文件系统
- /tmp: 临时文件夹
- /usr: 用户应用目录
- /var: 变量数据目录

## 环境变量

- 用户环境: vim .bash\_profile
- 系统环境: vim /etc/bashrc
- PATH: PATH变量中路径用冒号:分开
- source: 常用source .bash\_profile或. .bash\_profile重新加载环境变量

‍

---

‍

# 二、语法

严格区分大小写, 空格不能省, 适配正则表达式

## 参数

- 一个`-`​为缩写, 两个为完整
- 可拼接, 如-a和-l可写为-al
- -y: 一律yes
- -type options: d、f……

## 注释

```bash
 # 单行注释
 
 : <<'END'    # 特殊重定向符, 告诉shell忽略END之间的所有内容, END是自定义的标识符, 可替换
 这是多行注释      # shell本身没有多行注释语法, 故而用伪多行注释来模拟 
 它会被忽略
 END
```

## 符号

- ​`\\`​: 换行符
- ​`|`​: 管道符, 将前一个命令的输出作为后一个命令的输入

  eg: `who | wc -l`​: 返回登录的人数

  - ​`｜ xargs`​: 将前一个命令的多项输出作为多参数传递给下一命令, 相当于用空格拼接

    eg: `rpm -qa | grep 'mysql' | xargs rpm -e`​
- ​`&&`​: 将两个命令放在一起, 若前成功则执行后, 前失败则后不执行

  eg: `make && make install`​
- ​`||`​: 两个命令放在一起,  若前成功则后不执行 若前失败则后执行
- ​`;`​: 多个命令用分号隔开,  不管前成功与否都执行后
- ​`>`​: 输出重定向, 左边数据写入右边, 覆盖写'w'模式
- ​`>>`​: 追加输出重定向, 'a'模式
- ​`2>`​: 错误输出重定向, 正常信息和错误信息放在两个文件中
- ​`<`​: 输入重定向, 从右边读数据
- ​`$`​: 表示后面为变量

# 三、磁盘管理

- df: 列出文件系统的磁盘使用状况
- fdisk: 磁盘分区表操作
- parted: 磁盘分区工具
- mkfs: 格式化文件系统
- fsck: 文件系统检查
- dd: 转换/拷贝文件
- mount/umount: 挂载/卸载
- mkswap/swapon/swapoff: 创建/激活/关闭交换分区

# 四、辅助命令

- ​`whatis`​

  - ​`makewhatis`​, 更新whatis数据库
- ​`man`​命令: 显示完整帮助手册, 按q退出, 回车下一行, 空格下一页, b上一页
- ​`命令 --help`​
- info/apropos: 查看帮助文档
- clear: 清空屏幕
- whereis/which: 显示程序名所在完整路径
- history: 显示历史命令, 生成有编号的历史清单

  - ​`!n`​: 重复第n号命令
  - ​`history -c`​: 清除历史命令, 清除后, `!n`​不再适用
- cal: 显示日历, 默认显示当月

  ​`cal [options] [[[day] month] year]`​

  - options

    - -y --year
    - -1 --one
    - -3 --three
    - -s --sunday
    - -m --month/monday
    - -v --version
    - -h --help
  - eg: 

    - cal -y 2017: 显示全年, 不写年份year则默认今年
    - cal 9 2017: 显示指定年月
- date: 显示日期
- md5sum: 校验码
- echo: 打印
- alias: 设置别名, `alias 别名='命令'`​
- unalias: 取消别名

‍

---

‍

# 五、系统命令

- ​`logout`​/`exit`​: 退出登录
- ​`last`​: 查看最近登录的信息

  - ​`lastb`​: 显示尝试登录的信息(攻击者)
- hostname/uname: 查看系统和主机名

  - hostnamectl set-hostname newname: 关闭连接更安全, 需要重启
- hostnamectl: 查看当前系统信息
- cat /etc/shells: 查看当前系统下有哪些shell
- echo $SHELL: 查看当前系统正在使用的shell
- ​`ps`​: 查看自己使用的shell及进程信息
- ​`shutdown [options] [time] [wall]`​: 关机

  - options

    - -P --poweroff
    - -h: 关机
    - -r --reboot: 重启
    - -H --halt
    - -c: 取消关机
    - --no-wall: 关机无警告
  - time

    - now
    - 12:37 : 指定时间
    - +10 : 十分钟后
- ​`halt`​: 关闭系统, 等同于shutdown -r now
- ​`init`​

  - ​`init 0`​: 关机
  - init 1: 单用户
  - init 2: 单用户
  - init 3: 多用户带网络, 默认
  - init 4: 无操作
  - init 5: 启动图形化系统
  - init 6: 重启
- ​`reboot`​: 重启

‍

---

‍

# 六、文件操作命令

- ​`pwd`​: 当前目录, print working directory
- ​`cd`​: 切换路径, change directory

  - ​`cd`​ 或 `cd ～`​: home目录
  - ​`cd /`​: 根目录
  - ​`cd -`​: 上一次目录
  - ​`cd ..`​: 切换到上一级目录
  - ​`cd .`​: 切换到当前目录
  - ​`cd ../..`​: 切换到上级的上级目录
- ​`ls [options] [file]`​: 列出文件内容, list directory contents

  - 蓝色为文件夹, 浅蓝色为链接, 黑色为文件, 前缀.为隐藏内容, 默认不显示隐藏内容

  - options

    - -a --all: 显示隐藏内容, `.`​、`..`​本质是当前目录和上级目录, 平时隐藏, 隐藏文件以`.`​开头
    - -h: 智能地显示文件大小, 搭配-l
    - -l: 显示长格式内容(即详细信息), 可省略为ll

      - 第一位

        - ​`-`​: 文件
        - ​`d`​: 文件夹
        - ​`l`​: 链接/快捷方式
      - rwx三位一组, 共九位三组, 所有者权限、同组用户权限、其他用户权限

        - ​`r`​: 读权限
        - ​`w`​: 写权限
        - ​`x`​: 执行权限
        - ​`-`​: 无对应位置的权限
    - -R --recursive: 递归查看
    - -S: 排序显示, 根据文件大小, 默认升序
    - -r: 反转显示
  - file: 显示指定文件的内容
- ​`mkdir [options] directory`​: 创建文件夹

  - options

    - -p --parents: 允许在父文件夹不存在时创建文件
  - 可同时创建多个: `mkdir -p abc/{hello, workd}/{1,2,3}`​

    - 创建同级的hello和world两个文件夹, 每个文件夹均有1, 2, 3三个文件夹

      注意此处不可以有空格
- ​`touch [options] file`​: 创建空文件

  - file

    - ​`touch 1.txt`​: 在当前目录下创建txt
    - ​`touch /root/1.txt`​: 在指定目录创建txt
    - ​`touch ./demo/1.txt`​: 在上层目录创建, 注意上层文件必须存在
- rename: 文件重命名
- ​`rm [options] file`​: 删除文件/文件夹, 默认删除空文件夹, 可以使用通配符\*

  - options

    - -f: 强制删除
    - -i: 交互删除, 默认
    - -r: 递归式删除
    - -rf: 强制递归删除
- ​`rmdir dir`​: 删除空目录
- ​`cp [options] source destination/newname`​: 拷贝文件到指定位置, 重命名

  - options

    - -r: 递归式拷贝, 可以拷贝文件夹
    - -i: 询问是否覆盖已存在文件
    - -v: 显示拷贝后的路径描述
- ​`mv [options] source directory/newname`​: 移动到指定位置, 重命名

  - 操作文件夹不需要-r
  - -v: 显示移动后的路径描述
  - -i: 交互式提示
- tee: 多重定向, `ls | tee -a ls.txt`​显示ls的结果并追加输出到ls.txt文件中
- ​`tar [options] [file] [target]`​: 解归档

  - 解归档: 将一个归档文件, 如`.tar`​、`.zip`​、`.rar`​中的内容提取出来

    归档: 将多个文件和目录组合成一个单一文件, ==不一定压缩==

    压缩: 通过算法减小文件大小

    解压: 将压缩文件中的内容还原

    gzip: 压缩率低, 快

    bzip2: 压缩率高, 慢
  - options

    - -z: 压缩/解压.gz文件
    - -j: 压缩/解压.bz2文件
    - -v: 过程可视
    - -c name.tar: 创建归档
    - -x: 解归档
    - -f: 指定被操作文件, 必须放在所有option之后
    - -C path: 解归档到指定位置
  - 常用

    - -cf: `tar -cf hello.tar ./*`​
    - -xvf
- 解压缩

  - ​`gzip file`​: 压缩文件
  - ​`gunzip`​: 解压文件
  - ​`xz`​: 

    - -z: 压缩
    - -d: 解压
    - -0、-1、……、-9: 压缩级别
- ​`ln [options] [target] [link name]`​: 创建链接

  - 最好使用绝对路径创建连接, 否则移动链接后, 路径失效导致链接失效
  - options:

    - -s: 软链接
  - 常用: `ln -s /usr/local/python3.11/bin/python3 /usr/bin/python3`​
- readlink: 查看链接

‍

# 七、文件查看命令

- ​`tree [file]`​: 查看文件树

  - 可结合more: `tree file | more`​
- ​`basename`​/`dirname`​: 显示文件或目录
- file: 判断文件类型
- `cat [options] [file]`​: 拼接文件, 单参数为查看文件全部内容, 适合小文件, 不适合查看二进制内容

  - -n: 显示行号

  - 常用: `cat -n file | more`​
- ​`tac`​: 倒转行号显示内容, 每行内容不变
- ​`rev`​: 每一行的内容反转显示, 但是行号不变
- ​`head`​/`tail`​: 查看文件前部/末尾, 默认显示10行, `-n`​: 查看n行
- ​`more`​/`less`​: 慢加载文件以方便查看, 无行号

  - 按回车下翻, 按空格翻页, 按q退出, j下移, k上移, f下一页, b上一页
- ​`od`​: 用八进制呈现内容, 适用于查看二进制内容
- ​`wc`​: 返回文件的行数、单词数、字符数, `-l`​: 只返回行数
- iconv: 编码转换
- ​`find [path] [options] [expression]`​: 搜索文件, 可使用通配符

  - options

    - -name: 根据文件名字搜索
    - -size: 根据文件大小搜索
    - -atime: 根据文件最后访问时间搜索
    - -mtime: 根据文件最后修改时间搜索
    - -ctime: 根据文件创建时间搜索
    - -type: 根据文件类型搜索
    - -delete: 找到之后删除
  - expression

    - ​`+n`​: n以上
    - ​`-n`​: n以下
  - 通配符

    - ​`*`​: 0个或多个任一字符
    - ​`?`​: 任一字符
- ​`grep [options] pattern [file]`​: 搜索, 一般结合管道

  - pattern中间有空格时要加引号

  - options

    - -i: 忽略大小写
    - -o: 只输出匹配的部分, 而不是整行
    - -v: 反向匹配, 显示未匹配的行
    - -n: 显示匹配行号
    - -E: 使用正则表达式匹配

      - ​`^`​: 指定字符开头, `$`​: 指定字符结尾, `.`​: 匹配任一非换行符
- ​`egrep`​: 正则搜索

‍

---

‍

# 八、文件处理命令

- ​`uniq`​: 去除相邻的重复值, `-c`​: 记数

  - ​`sort file | uniq`​: 全局去重
- ​`sort`​: 排序, 默认升序

  - -r: 降序
  - -nr: 根据数字排序
- ​`tr`​: 替换

  - ​`tr 'old' 'new' < 1.txt`​
  - ​`tr '[aeiou]' '*'`​: 可操作字符集
  - ​`tr '[:lower:]' '[:upper:]' < 1.txt`​: 大小写转换
  - --help: 查看各种写法
  - -s
- ​`paste`​: 左右拼接, 默认以制表符分隔

  - ​`-d *`​: 指定分隔符, 不加引号

    - eg: `-d , > sales.csv`​, 逗号直接拼出csv文件
- ​`cut`​: 竖直拆分

  - ​`-d *`​: 指定分隔符
  - ​`-f n`​: 要查看的列编号
  - eg: `cut -d , -f 2 filename`​, 用逗号拆分后查看第2列
- split: 拆分文件
- ​`sed '操作' filename`​: 字符流编辑器, 不改变原文件, 只是将新内容**输出到终端**

  - 操作: 

    - ​`na text`​: 在第n行后插入text
    - ​`ni text`​: 在第n行前插入text
    - ​`nd`​: 删除第n行
    - ​`m,nd`​: 删除从m到n行
    - ​`s#old#new#g`​: s表替换, old换为new, g为全局替换, 无g则只替换每行第一个
- ​`awk '{操作}' filename`​: 模式匹配和处理语言

  - BEGIN / END {操作}: 操作会在读文件前或读完所有之后执行, 一次性

    ​`awk 'BEGIN{print '名称','数量','价格','时间'}' file`​: 制作表头

    ​`awk 'END{print 'Game Over!'}' file`​
  - 操作: 每读一行执行一次

    - ​`-F ,`​: 指定分隔符为逗号
    - ​`print $1,$2`​: 输出第1、2列, \$指定列编号, \$0为整行, $NF为最后一列
    - 筛选: `if ($2 >= 200) print $1`​
    - 分组聚合

      - ​`{x[$3] += $2} END{for (i in x) print i,x[i]}`​: 以第3列分组, 累加第2列, 最后输出
      - ​`{x[$3] += 1} END{for (i in x) print i,x[i]}`​: 以第3列分组, 计数(每个值出现的次数), 最后输出
    - ​`……｜sort -nr ｜ head -n`​: 取前n名

‍

---

‍

# 九、任务管理命令

## 网络管理

- ​`ssh`​: `ssh root@39.106.134.248`​公有, `ssh root@172.22.53.165`​私有, pwd: 大i7
- `netstat`​: 查看网络端口使用情况

  - -n
  - -t: 查看tcp协议
  - -l: 查看监听状态
  - -p: 查看进程
- ​`nc`​: Netcat, 网络调试和数据传输的工具

  - ​ **​`-n`​**​: 不解析域名(不查询DNS), 直接使用提供的IP地址, 更快
  - ​ **​`-v`​**​: 启用详细模式(verbose mode), 会输出更多的信息, 例如连接状态或错误消息, `-vv`​提供更加详细的调试信息

  ```bash
  # 测试某个端口是否开放
  nc -nv 192.168.1.1 80  # 测试某个端口是否开放
  # 输出会显示连接是否成功或超时

  # 简单的端口扫描, 范围1到1000
  nc -nv 192.168.1.1 1-1000

  # 作为服务器监听
  nc -l -p 1234
  # 在本地端口1234上监听, 等待连接
  ```
- ​`ss`​: 同netstat
- ​`ifconfig`​: 旧的网络配置工具, 查看内网地址, 可指定查看的内容: `ifconfig eth0/lo`​
- ​`ip addr`​: 新的网络配置工具, 查看网络IP地址

  - 私有ip

    - 10.0.0.0～10.255.255.255
    - 172.16.0.0～172.31.255.255
    - 192.168.0.0～192.168.255.255
- ​`wget url`​: 

  - -O: 指定名称, 下载到指定的目录
  - -b: 后台下载模式
  - -r: 递归下载
- ​`mail`​: 发送和接受邮件
- ​`ping url/ip`​: 检测网络可达性, `-c n`​: 检测n次
- ​`route`​: 显示或管理路由表
- ​`tcpdump`​: 网络监听抓包
- ​`nslookup url`​: 解析ip地址

## 文件管理

- Termius软件操作
- ​`scp 目标文件 目标地址`​: 远程安全拷贝, 有远程主机时需要用户和ip, 冒号分隔路径

  - -r: 递归拷贝整个目录
  - eg: `scp ./1.txt root@10.11.51.82:/root/`​
- ​`sftp root@10.11.51.82`​: 安全传输, 默认进去用户主目录, 连接成功后可下步操作

  - ​`ls`​/`lls`​: 显示远端/本地目录列表
  - ​`cd`​/`lcd`​: 切换远端/本地路径
  - ​`mkdir`​/`lmkdir`​: 创建远端/本地目录
  - ​`pwd`​/`lpwd`​: 显示远端/本地当前工作目录
  - ​`get filename`​: 下载
  - ​`put filename`​: 上传
  - ​`bye`​/`exit`​/`quit`​: 退出sftp
- ​`rsync`​: 实现文件的自动同步

## 进程管理

- ​`ps [options]`​: 显示进程

  - options

    - -e: 显示所有进程
    - -f: 全格式
    - -h: 不显示标题
    - -l: 长格式
    - -w: 宽输出
    - -a: 显示一个终端的所有进程
    - -r: 只显示正在进行的进程
    - -u: 显示当前用户进程和内存使用情况
    - -x: 显示没有控制终端的进程
    - -sort: 按照列名排序
  - 常用: `ps -ef`​、`ps -aux`​、`ps -ef | grep 'ssh'`​
- ​`pstree`​: 进程树, `-p`​: 显示进程号
- ​`kill [options] pid`​: 终止进程, pid为进程id, 可用netstat/ps查询

  - options

    - -1/ SIGHUB: 挂起
    - -2 / SIGINT: 中断
    - -9 / SIGKILL: 强制关闭
    - -15 / SIGTERM: 默认, 发送关闭信号
    - -19 / SIGSTOP: 暂停, 放到后台
- ​`pkill name`​/`killall`​: 根据进程名关闭进程, `-f`​: 精确匹配, 只有进程名完全匹配才关闭

  - eg: `pkill java`​, 带有java的进程均被关闭
- ​`pgrep name`​: 根据进程名查询进程id
- ​`top`​: 任务管理器, 按n显示第n个cpu的进程, 按q退出

  - -i: 不显示空闲和僵尸进程
  - -u: 指定用户
  - -o: 指定按照哪个列顺序
  - -p: 显示指定进程的信息
  - -c: 显示进程的整个路径
  - -d: 指定两次刷屏之间的间隔时间, 单位秒
- ​`htop`​
- ​`runlevel`​: 查看当前运行级别
- ​`jobs`​: 查看后台任务

  - -l: 显示进程号
  - -s: 显示暂停的进程
  - -r: 显示运行的进程
- ​`fg [%编号]`​: 将后台任务放到前台, 无编号则所有, 编号由jobs查看, 百分号在前
- ​`bg [%编号]`​: 让进程在后台继续运行, 编号由jobs查看, 百分号在前
- ​`nice`​/`renice`​: 调整程序/进程运行时优先级
- ​`nohup`​: 用户登出后进程继续运行, 不接受挂起信号运行, `kill -1`​杀不掉
- ​`命令`​ + `&`​: 将命令放到后台执行
- ​`strace`​: 跟踪进程系统调用情况

## 服务管理

- systemctl start xxx
- systemctl restart xxx
- systemctl stop xxx
- systemctl status xxx
- service 软件名 [start | stop | restart | reload | status]

服务名通常带后缀`d`​, 表示daemon守护进程

## 定时任务

- ​`at 23:10+5days`​: 指定时间执行后续代码

  - 回车后出现at\>, 编辑定时任务, ctrl+d结束任务编辑
- ​`atq`​: 查看定时任务队列
- ​`atrm 任务编号`​: 从队列删除待执行的任务, 编号用atq查看
- ​`crontab`​: 周期任务

  - -e: 编辑克隆表
  - 克隆表达式

    - 分 时 日 月 星期 操作
    - ​`*`​: 不限制
    - ​`,`​: 并列
    - ​`-`​: 范围
    - ​`m/n`​: 从m开始, 每隔n

## 系统诊断

- dmesg: 系统启动异常诊断
- sar: 查看系统活动信息
- free: 查看内存使用情况
- vmstat: 虚拟内存统计
- mpstat: CPU信息统计
- pmap: 查看进程使用内存状况
- iostat: 报告设备CPU和I/O统计信息
- lspci: 显示所有PCI设备
- ipcs: 显示进程间通信设施的状态

# 十、用户管理命令

- ​`who`​/`w`​: 查看登录信息

  - whoami: 查看自己当前用户
  - who -a: 详细信息
  - who -aH: 详细信息

- ​`useradd username`​: 添加用户

  - -d: 可指定用户主目录, 默认在/home/username
  - -g: 指定用户所属的用户组
- ​`userdel`​: 删除用户

  - -f: 强制删除, 包括已登录用户也可删除
  - -r: 删除用户目录下的文件
- ​`groupadd`​: 添加用户组
- ​`groupdel`​: 删除用户组
- ​`passwd [username]`​: 更改密码, 不写用户名则更改当前用户密码

  - -e username: 密码过期, 下次登录需要设置新密码
  - -l username: 锁定用户, 不能登录
  - -u username: 解锁用户
  - -d: 删除密码, 空密码
  - -i: 设置密码过期多少天后禁用该用户
- ​`su [username]`​: 切换用户, 提示符为#, 普通用户提示符\$, 省略参数默认切到root
- ​`sudo`​: 以超级管理员身份执行命令, `--list`​显示sudoers清单
- ​`visudo`​: 编辑sudoer清单
- ​`id`​: 显示用户相关信息
- write/wall [username] [content]: 给其他用户发消息
- mesg: 查看是否接收其他用户的消息, `-n`​: 设置为不接收, `-y`​: 设置接收
- ​`chage`​: 查看和修改密码的有效期

  - -l username: 查看密码有效期
  - -w: 设置n天前发出过期警告
  - -d: 设置密码有效期, 最后一天
  - -M n: 设置有效期天数为n天
- ​`chmod [u|g|o] [+|-] [r|w|x] filename`​: 使用对应用户的分数, 改变文件的权限

  - u: 所有者 g: 同组用户 o: 其他用户 a: 所有人
  - 数字法:  `chmod nnn filename`​: n为用户组的权限分值和

    - 三位n则一一对应改权限, 两位n则改后两组权限, 一位n则改其他用户组
    - r: 4分  w: 2分  x: 1分 -: 0分
    - 7为rwx, 5为r-x, 777为所有用户所有权限

  - eg: `chmod u+x,g+x,o+x hello.txt`​、​`chmod +x test.py`​
- ​`chown username[:groupname] file`​: 改变文件所属者(和用户组), 有用户组时冒号分隔

  - -R: 递归式, 文件夹及其中文件均改变
- ​`chgrp groupname file`​: 改变文件用户组
- 查看用户目录: `cd /home`​ + `cd username/`​ + `ls`​

# 十一、vim编辑器操作

- 打开: `vim/vi file1 file2 file3……`​, 文件可以不存在
- 编辑: 

  - i: 在当前位置前插入  I: 在行首插入
  - a: 在当前位置后插入  A: 在行尾插入
  - o: 在下方增加新行并编辑   O: 在上方增加新行并编辑
  - ESC: 回到命令模式
- 光标移动

  - G: 移动到最后一行, `行号+G`​: 转到指定行
  - gg: 移动到文件开头
  - 0: 移动到行首
  - \$: 移动到行尾
  - h: 光标左移, `n+h`​: 向左移动n格
  - l: 光标右移, `n+l`​: 向右移动n格
  - j: 光标下移, `n+j`​: 向上移动n格
  - k: 光标上移, `n+k`​: 向上移动n格
  - w: 移动到下一个单词的开头
  - e: 移动到下一个单词的末尾
- 操作

  - 翻页

    - ctrl+f: 下翻一页
    - ctrl+b: 上翻一页
    - ctrl+e: 下翻一行
    - ctrl+y: 上翻一行
  - 删除

    - dd: 删除当前行,  `ndd`​: 删除n行
    - d0 / d\$: 从光标删到行首/行尾
    - dnh / dnj: 从光标向左/下删n个字符
    - dw: 删一个单词
  - 复制

    - yy / Y: 复制光标所在行, `nyy`​: 复制n行
    - y0 / y\$: 从光标复制到行首/行尾
    - yw: 复制一个单词
  - 粘贴

    - p: 在光标后粘贴, `np`​: 粘贴n次
    - P: 在光标前粘贴
  - ​`.`​: 再次执行上一次的命令
  - ​`>>`​: 向右缩进
  - ​`<<`​: 向左缩进
  - u: 撤销
  - ctrl+r: 重做上一步撤销的操作
  - v: 选中部分可视
  - ZZ: 保存并退出
  - ​`%!sort`​: 对内容排序
- 末行模式

  - :w / w!: 保存, `: w + filename`​: 以指定名称保存文件
  - :q / q!: 退出/强制退出vim
  - :wq: 强制保存并退出
  - ​`:x`​: 保存退出
  - ​`:%d`​: 删除全文
  - :set nu / nonu: 显示/隐藏行号
  - :syntax on / off: 开启/关闭语法高亮
  - :set ts\=4: 设置制表键的空格数
  - :set ruler / noruler: 设置标尺
  - :set hls/set nohls: 设置启用/关闭搜索结果高亮
  - :set autoindent / noautoindent: 设置自动缩进
  - :set expandtab / noexpandtab: 是否制表键变空格
  - 使vim设置全局生效: ​`touch .vimrc`​ `vim .vimrc`​

    - ​`set ts=4 `​
    - ​`set nu `​
    - ​`set autoindent `​
    - ​`set ruler `​
    - ​`set expandtab `​
    - ​`syntax on`​
  - ​`/ + 查找文本`​: `/`​也可进入末行模式, 需提供正则表达式

    - n: 向前搜索, 查找下一个匹配项
    - N: 向后搜索, 查找上一个匹配项
  - ​`? + 查找文本`​: 从下往上查找

    - n: 查找上一个匹配项
    - N: 查找下一个匹配项
  - ​`: %s/old/new/g`​: 替换, 范围+文本+参数, eg: `:2,20s/old/new/c`​

    - 范围: 2, 20为指定范围, `1,$`​为全文
    - ​`s/`​为替换命令的开始
    - 文本: s/old/new, s为精确查找
    - 参数

      - /g: 全局替换, 若不加g则默认只替换每行的第一个
      - /c: 每次替换询问
      - /i: 忽略大小写匹配
      - /e: 忽略错误
    - 搜索后取消高亮

      - : nohls
  - 多文件编辑

    - ​`:ls`​: 返回所有编辑文件清单, 适用于vim了多个文件时
    - ​`:b n`​: 切到第n个文件
    - ​`:sp`​: 水平拆分窗口, `ctrl+ww`​: 光标在不同窗口间切换
    - ​`:vs`​: 垂直拆分窗口
    - ​`:qa`​: 退出所有
- 快捷操作

  - ​`!v`​: 把最近以v开头的文件再执行一遍
  - 变python脚本为直接可执行文件: 在开头增加解释器的路径

    eg:  `#!/usr/local/python3.11/bin/pyhton3`​

    - 必须在开头, 没有空格, 必须绝对路径
- 比较: `vim -d file1 file2`​, 不同的地方会高亮显示
- 映射快捷键: 

  - ​`:map <F4> gg10000dd`​, 命令模式下输入F4执行从第一行开始删除10000行代码的操作
  - ​`:inoremap __main if __name__ == '__main__':`​, 编辑模式输入`__main`​直接补全, i表示映射的键在编辑模式使用, nore表示不递归
- 录制宏: 命令模式下输入qa开始录制宏, 光标操作、编辑操作均被录制, 按q结束录制

  - ​`n@a`​播放宏n词, n可省略
  - a是寄存器的名字, 可选其他英文字母或0-9的数字

# 十二、包管理工具

- ​`rpm`​: redhat package manager, 适用于`.rpm`​格式的软件包

  - -i: install
  - -v: verbose, 可视
  - -h: 校验完整性
  - -qa: 查询所有
  - -e xxx: 移除
  - 常用: rpm -ivh
- ​`yum`​: yellowdog updater modified, 适用于`.rpm`​格式的软件包

  - 搜索: yum search xxx
  - 安装: yum install -y xxx
  - 查看: yum list installed
  - 信息: yum info xxx
  - 卸载: yum erase/remove -y xxx
  - 更新: yum update xxx, 无包名则更新全部
  - 检查更新: yum check-update
  - 使用

    - 启动: systemctl start xxx
    - 停止: systemctl stop xxx
    - 重启: systemctl restart xxx
    - 查看状态: systemctl status xxx
- ​`apt-get`​: 使用于`.deb`​格式的软件包

  - 卸载: `sudo apt-get remove 安装包名`​
- 离线安装: deb文件格式安装, 用dpkg命令: ​`sudo dpkg -i deb安装包`​

  - 卸载: `sudo dpkg -r 安装包名`​
- ​`make && make install`​: 源代码构建安装

# 十三、常用软件

## centos

- 设置root密码: `sudo passwd root`​
- 设置ssh: 

  ```python
  yum install openssh-server 
  vim /etc/ssh/sshd_config 
  PermitRootLogin yes 
  service sshd restart
  ```

## python3

- 源代码构建安装
- 卸载旧的: yum erase -y python3
- 下载: wget ……
- 验证: md5sum Python-3.9.13.tgz
- 解归档: tar -zxf xxx
- 环境检查: gcc --version make --version
- 补包: 

  ```python
  yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel libdb4-devel libpcap-devel xz-devel libffi-devel libxml2
  ```
- 运行configure程序: 

  - cd Python-3.9.13
  - ./configure --prefix\=/usr/local/python39
- 构建安装: `make && make install`​
- 配置环境变量: `export PATH=$PATH:/usr/local/python39/bin`​

## mysql

- 下载: wget http://……

  - [全自动安装](https://oneinstack.com)​
  - 启动: sh install.sh
- 解归档: `mkdir mysql`​ + ​`tar -xvf xxx -C mysql/`​
- 按顺序安装: 先common后libs
- 操控: `service mysqld [start|stop|restart|reload|status]`​
- 配置支持group by: `vim /etc/my.cnf`​

  ```bash
  sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
  ```
- 忘记密码: `vim /etc/my.cnf`​, 找到mysqld, 首行写入skip-grant-tables

## nginx

web服务器

- 下载: yum search nginx yum install -y nginx
- 启动: systemctl start nginx
- 更改安全组规则, 增加80, 443端口

## finebi

- 下载: wget http:……
- 修改权限: 

  ```bash
  chmod utx,g+x,o+x linux_unix_FineBI5_1-CN.sh 
  chmod 755 linux_unix_FineBI5_1-CN.sh
  ```
- 运行脚本: `./linux_unix_FineBI5_1-CN.sh`​
- 访问: `http:公网IP地址:37799/webroot/decision`​

## hadoop

- 前置准备

  - 设置静态ip: ​`vim /etc/sysconfig/network-scripts/ifcfg-ens33`​

    - 此处要设置一个ping不通的ip

    - BOOTPROTO\="static" IPADDR\="10.11.51.82"
    - NETMASK\="255.255.255.0" GATEWAY\="10.11.51.1" DNS1\="8.8.8.8" DNS2\="114.114.114.114"
    - sudo systemctl restart NetworkManager: 重启网卡
  - 主机自信任

    - ​`ssh-keygen`​: 生成密钥
    - ​`ssh-copy-id -i .ssh/id_rsa.pub root@10.11.51.82`​: 发送公钥, 信任主机
  - 防火墙

    - 查看运行状态: sudo firewall-cmd --state
    - 关闭防火墙: 一次性的, 重启还会开启

      - sudo systemctl stop firewalld
      - sudo systemctl start firewalld
    - 开机不启动: sudo systemctl disable firewalld
    - 开放端口

      - sudo firewall-cmd --permanent --add-port\=3306/tcp
      - sudo firewall-cmd --permanent --add-port\=50070/tcp
      - sudo firewall-cmd --permanent --add-port\=9000/tcp
    - 重新加载配置: sudo firewall-cmd --reload
  - 下载jdk

    - 把jdk放在/usr/local/jvm中解压
    - 编辑环境变量: `vim /etc/prifile`​

      - export JAVA\_HOME\=/usr/local/jvm/jdk1.8.0\_202 

        export JRE\_HOME\=\${JAVA\_HOME}/jre 

        export CLASSPATH\=.:\${JAVA\_HOME}/lib:\${JRE\_HOME}/lib 

        export PATH\=\${JAVA\_HOME}/bin:\$PATH
      - ​`source /etc/profile`​
      - ​`java --version`​
- 下载: https://hadoop.apache.org/releases.html
- 配置

  - 环境变量: `vim /etc/profile`​

    ```bash
    export HADOOP\_HOME\=/usr/local/hadoop-2.7.6 
    export PATH\=\$HADOOP\_HOME/bin:\$HADOOP\_HOME/sbin:\$PATH 
    export HADOOP\_COMMON\_LIB\_NATIVE\_DIR\=\$HADOOP\_HOME/lib/native
    ```

    - ​`source /etc/profile`​
  - 修改jvm路径

    ```bash
    cp /usr/local/hadoop-2.7.6/etc/hadoop/hadoop-env.sh /usr/local/hadoop-2.7.6/etc/hadoop/hadoop-env.sh.back
    vim /usr/local/hadoop-2.7.6/etc/hadoop/hadoop-env.sh 
    # export JAVA_HOME=/usr/local/jvm/jdk1.8.0_202
    ```
  - 修改core-site.xml: ​`vim /usr/local/hadoop-2.7.6/etc/hadoop/core-site.xml`​

    ```xml
    <configuration> 
    	<property> 
    		<name>hadoop.tmp.dir</name> 	
    		<value>file:/usr/local/hadoop-2.7.6/tmp</value> 	
    		<description>Abase for other temporary directories.</description> 
    	</property> 
    	<property> 	
    		<name>fs.defaultFS</name>
     		<value>hdfs://0.0.0.0:9000</value> 
    	</property> 
    </configuration>
    ```
  - 修改hdfs-site.xml: `vim /usr/local/hadoop-2.7.6/etc/hadoop/hdfs-site.xml`​

    ```xml
    <configuration>
    	<property> 	
    		<name>dfs.replication</name> 	
    		<value>1</value> 
    	</property> 
    	<property> 	
    		<name>dfs.namenode.name.dir</name> 	
    		<value>file:/usr/local/hadoop-2.7.6/tmp/dfs/name</value> 
    	</property> 
    	<property> 	
    		<name>dfs.datanode.data.dir</name> 	
    		<value>file:/usr/local/hadoop-2.7.6/tmp/dfs/data</value> 
    	</property> 
    </configuration>
    ```
  - 修改mapred-site.xml: 

    ```bash
    cp mapred-site.xml.template mapred-site.xml 
    vim /usr/local/hadoop-2.7.6/etc/hadoop/mapred-site.xml
    ```

    - ```xml
      <configuration> 
      	<property> 		  
      		<name>mapreduce.framework.name</name>
       		<value>yarn</value> 	  
      	</property> 
      </configuration>
      ```
  - 修改yarn-site.xml: ​`vim /usr/local/hadoop-2.7.6/etc/hadoop/yarn-site.xml`​

    ```xml
    <configuration> 
    	<property> 
    		<name>yarn.nodemanager.aux-services</name> 
    		<value>mapreduce_value</value> 
    	</property> 
    </configuration>
    ```
- 操作

  - 初始化: hdfs namenode -format -Y, 第一次开启/清空数据时使用
  - 开启

    - start-dfs.sh
    - start-mapreduce.sh
    - start-yarn.sh
    - start-all.sh: 开启全部
  - 关闭

    - stop-dfs.sh
    - stop-all.sh

## hive

- 下载: `https://mirrors.tuna.tsinghua.edu.cn/apache/hive/`​, 放到`/usr/local`​

  - 解压: tar -zxvf  xxx
  - 修改名称: mv xxx hive-1.2.2
- 配置: ​`vim /etc/profile`​

  - export HIVE\_HOME\=/usr/local/hive-1.2.2 

    export PATH\=\$PATH:\$HIVE\_HOME/bin:\$HIVE\_HOME
  - ​`source /etc/profile`​
- 关闭报警提示: ​`vim /usr/local/hadoop-2.7.6/etc/hadoop/hadoop-env.sh`​ 

  - ​`export HADOOP_OPTS="-Djava.library.path=${HADOOP_HOME}/lib/native"`​

## spark

- 下载: `https://mirrors.tuna.tsinghua.edu.cn/apache/spark/`​, 放到/usr/local

  - 解压
  - 重命名: mv xxx spark-2.4.7
- 配置

  - ​`vim /etc/profile`​

    ```bash
    export SPARK_HOME=/usr/local/spark-2.4.7 
    export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
    ```

    - ​`source /etc/profile`​
  - ​`vim /usr/local/spark-2.4.7/conf/spark-env.sh`​

    ```bash
    JAVA_HOME=/usr/local/jvm/jdk1.8.0_202 
    SPARK_WORKER_MEMORY=4g  	#spark计算节点的大小 
    SPARK_LOCAL_IP=localhost
    ```
  - ​`vim /usr/local/spark-2.4.7/conf/log4j.properties`​, 把所有的INFO改为ERROR
  - ​`vim /usr/local/hive-1.2.2/bin/hive`​, 找到下面代码: 

    ```bash
    if [[ -n "\$SPARK\_HOME" ]] 
    	then sparkAssemblyPath='ls ${SPARK_HOME}/lib/spark-assembly-*.jar' 
    		 CLASSPATH="${CLASSPATH}:${sparkAssemblyPath}" 
    fi
    ```

    将/lib/spark-assembly-\*.jar替换成/jars/spark-\*.jar

## anaconda

- 环境变量: `vim /etc/profile `

  ```bash
  export PATH=/usr/local/Anaconda3/bin:$PATH
  export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.7-src.zip 
  export SPARK_CLASSPATH=/usr/local/spark-2.4.7/jars 
  __conda_setup="$(CONDA_REPORT_ERRORS=false '/usr/local/Anaconda3/bin/conda' shell.bash hook 2> /dev/null)" 
  if [$? -eq 0 ]; then 
  	\eval "$__conda_setup" 
  else 
  	if [ -f "/usr/local/Anaconda3/etc/profile.d/conda.sh" ]; then 
  		. "/usr/local/Anaconda3/etc/profile.d/conda.sh" 
  		CONDA_CHANGEPS1=false conda activate base 
  	else 
  		\export PATH="/usr/local/Anaconda3/bin:$PATH" 
  	fi   
  fi unset __conda_setup
  ```
  - ​`source /etc/profile`​
- 配置jupyter可远程访问

  - 配置密码: 输入`ipython`​

    ```python
    from notebook.auth import passwd
    passwd('123456') 
    exit()
    ```
  - 生成Jupyter配置文件

    - Jupyter notebook -- generate-config
    - c.NotebookApp.allow\_root\=true
    - c.NotebookApp.ip \= '\*': 允许任何IP都可以访问
    - c.NotebookApp.open\_browser\=False: 服务器并没有浏览器可以供其打开
    - c.NotebookApp.password\=u'sha1:9a7dc80f8684:f6bd5c6f151eb3afe66208703cfc65777e0fb133'
    - c.NotebookApp.port\=8888: 监听端口设置为8888, 可自定义
    - c.ContentsManager.root\_dir\=u'/root/document': 设置jupyter打开的默认文件夹, 必须存在
    - c.NotebookApp.allow\_remote\_access\=True: 允许远程访问
  - 启动: jupyter notebook --allow-root
  - 配置jupyter开启脚本: `vim start-jupyter.sh`​

    ```bash
    nohup jupyter notebook --allow-root > /root/.jupyter/output.log 2>&1 & echo 'jupyter is running'
    ```
  - 配置jupyter关闭脚本: ​`vim stop-jupyter.sh`​

    ```bash
    PID=$(ps -aux | grep jupyter-notebook | grep -v grep | awk '{print \$2}') 
    kill -9 $PID 
    echo "jupyter is die"
    ```
