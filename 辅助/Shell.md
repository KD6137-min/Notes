# Shell

```bash
# 查看系统内的shells
cat /etc/shells
/bin/sh		# 切换到sh，影响$0,不影响$SHELL
exit	# 退出

# 查看环境变量
echo $HOME
echo $PATH		# 系统查找路径
echo $SHELL
echo $0			# 查看正在执行的脚本的shell,$1、$2等表示传入的第一、第二个参数
```

注释：`#`

`$#`：传递给脚本/函数的位置参数的个数

`$?`：上一命令的退出状态码，0常表示无错误，非0表示有错误

`$*`：传递给脚本/函数的位置参数，双引号包裹时为一个整体

`$@`：传递给脚本/函数的位置参数

`$$`：当前Shell进程的进程ID

`$!`：最后一个后台命令的进程ID

`$0`：当前脚本的名称

`$1-n`：脚本/函数的位置参数

## Shell脚本

扩展名可任意甚至没有，但一般约定俗成`.sh`

执行前记得`chmod +x script.sh`

```bash
#！/bin/zsh 		# shebang，声明要用哪个shell

# 函数：检查一个数是否为素数
is_prime() {
	local num=$1	# $1表示函数的第一个参数，脚本变量默认为全局，局部变量需声明local
	if [ $num -lt 2 ]; then		# lt小于，le小于等于，gt、ge、eq、ne
		return 1	
	fi
	for ((i=2; i*i<=num; i++)); do	# for语法严格，两个()括住三个表达式
		if [ $((num % i)) -eq 0 ]; then
			return 1
		fi
	done
	return 0
}

# 主程序开始
read -p "请输入一个正整数：" number

if is_prime $number; then
	echo "$number 是素数"
else
	echo "$number 不是素数"
fi
```



## 脚本的执行

需要加`sh`执行的情况：

- 脚本无执行权限时：执行`./script.sh`会失败，可通过`sh script.sh`执行，`sh`命令显式调用Shell解释器执行文件，无需x权限
- 脚本未声明Shebang时，或临时切换解释器时：可通过`sh`命令显式调用Shell解释器

不加`sh`的情况：

- 脚本有x权限，直接通过`./script.sh`或绝对路径执行，系统自动根据Shebang选择解释器
- 使用`source script.sh` 或`. script.sh`，可在当前Shell进程执行脚本，影响当前环境变量，脚本变量会保留在当前会话

## 传参

```bash
./script.sh param1 param2	# 两个位置参数
```

`param1`和`param2`作为参数传给`$1`和`$2`

```bash
#!/bin/bash

name=$1
channel=$2
echo "你好$name, 欢迎来到$channel "
```

## 作用域

```bash
name=老杨		   # 定义当前会话的变量
echo $name		# 输出老杨
./script.sh 	# 用到$name，无输出，因为脚本使用全局变量

export name=老杨 # 用export命令将变量声明为全局，shell会话关闭后清空
./script.sh		# 正常输出

# 定义为永久环境变量，需编辑~/.bashrc
vim ~/.bashrc
# 写入export name=老杨
source ~/.bashrc
./script.sh		# shell重启后仍可正常输出
```



## shell配置文件

`.bash_profile`：仅在用户登录时执行一次，通常会调用`.bashrc`

`.profile`：

`.bashrc`： 每次打开终端时执行，推荐放入环境变量

> /etc/bash/中的bash.bashrc是对所有用户生效的



## 随机数

```bash
shuf -i 1-10 -n 1
```

`-i m-n`：范围从m到n

`-n`：生成的数量

```bash
# script.sh:

#！/bin/bash
number=`shuf -i 1-10 -n 1`		# 将命令结果赋值给变量需用``或$()包裹命令
# number=$(shuf -i 1-10 -n 1) 	# 推荐方式，可读性更好
echo $number
```

## if语句

```bash
if condition; then
	语句
elif condition; then
	语句
else
	语句
fi
```

condition可为`[]`、`[[]]`、`(())`：

- `[]`：最基本的条件测试表达式
- `[[]]`：扩展测试命令，比`[]`更强大
- `(())`：数学表达式，支持常见的四则运算

> if语法严格，括号两头必须有空格

