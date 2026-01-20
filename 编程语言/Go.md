# Go

Go = C + Python, 既有静态语言的运行速度, 也有动态语言的快速开发

内存自动回收、天然支持并发、基于CPS并发模型、管道通信机制、函数可返回多个值

区分大小写、无分号、切片slice、延时执行defer、没有闲置变量

‍

组织程序结构

package xxx: 把本文件归属到xxx包, 一个文件归属一个包, 不能单独存在

import "xxx": 引入xxx包

每个项目可有一个或多个模块(一般为一个)

一个模块中必须有一个main包, main包中必须有且只有一个main函数

‍

编译

```bash
go build main.go 	// 编译, 生成可执行文件

go run main.go 		// 编译并运行
```

‍

注释: 同C, 不许嵌套

```go
// 单行注释     // 官方推荐
/* 多行注释 */  // 快捷键command+/
```

‍

函数声明关键字func

主函数main, 程序入口, 同C

```go
fmt.Println() 		// 结尾符为换行, 多参数间隔符为空格, 不支持占位符

fmt.Print()			// 无结尾符, 多参数间隔符为空格, 不支持占位符

fmt.Printf()		// 格式化输出, 用占位符精准控制输出格式
```

‍

转义字符

```go
\t 		// 制表符
\n		// 换行符
\\		// 一个\
\"		// 一个"
\r		// 一个回车
```

‍

占位符

|占位符|用法示例|说明|
| --------| ----------| -------------------------|
|​`%s`​|​`fmt.Printf("Hello, %s!\n", name)`​|字符串|
|​`%d`​|​`fmt.Printf("Number: %d\n", num)`​|整数|
|​`%f`​|​`fmt.Printf("Float: %.2f\n", pi)`​|浮点数|
|​`%t`​|​`fmt.Printf("Boolean: %t\n", true)`​|布尔值|
|​`%v`​|​`fmt.Printf("Value: %v\n", value)`​|变量的默认格式|
|​`%#v`​|​`fmt.Printf("Value: %#v\n", value)`​|变量的详细格式(Go 语法)|
|​`%x`​|​`fmt.Printf("Hex: %x\n", num)`​|16 进制表示|
|​`%p`​|​`fmt.Printf("Pointer: %p\n", &ptr)`​|指针地址|
|​`%c`​|​`fmt.Printf("Character: %c\n", ch)`​|字符|
|​`%n`​|​`fmt.Printf("Number of bytes written: %n", &n)`​|用于获取写入的字节数|
|​`%T`​|​`fmt.Printf("a的类型为%T", a)`​|对应值的类型|

‍

变量

命名规则同C, 定义后必须要使用

```go
// 1. var 变量名 type
var name string = "张三" 	// 有初始值
var age int					// 无初始值
var isOK bool
var name, age = "张三", 20 	// 多重赋值, 类型推导
var (						// 批量定义
	username string
	age int = 34
	gender string
)

// 2. 短变量声明法
username := "张三"
```

匿名变量

多重赋值时忽略某值, 使用 `_`​ 表示匿名变量

不占命名空间, 不分配内存, 不存在重复声明

不能用于全局变量

‍

常量

const

```go
const pi = 3.14159
const (
	pi = 3.14			// 批量声明
	e = 2.71828
)

const (					// 若下值省略, 表示值同上
	n1 = 100
	n2
	n3
)
```

iota

常量计数器, 只能在常量表达式中使用

每次在const关键字出现时重置为0(const内部第一行之前即为0), const每新增一行常量声明将使iota计数一次(理解为行索引, 在const的第几行就是几)

出现下一个const关键字则iota再次初始化为0, 没有跨cosnt块的iota

const iota 可使用_跳过某些值

```go
const (
	n1 = iota		// n1 = 0
	n2				// n2 = 1
	_
	n4				// n4 = 3
)
```

中间插队

```go
const (
	n1 = iota		// n1 = 0
	n2 = 100 		// n2 = 100
	n3 = iota		// n3 = 2
	n4				// n4 = 3
)
```

多个iota在同一行

```go
const (
	n1, n2 = iota + 1, iota +2		// 1, 2
	n3, n4							// 2, 3
	n5, n6							// 3, 4
)
```

‍

强制 `{`​ 在行末

‍

格式化

```bash
go fmt main.go
```

‍

数据类型

基本数据类型: 整浮布字

复合数据类型: 数组、切片、结构体、函数、map、通道(channel)、接口

整型: 

- 有符号

  - int8
  - int16
  - int32
  - int64
- 无符号

  - uint8
  - uint16
  - uint32
  - uint64​

![截屏2024-12-17 14.17.12](../assets/截屏2024-12-17%2014.17.12-20241217141714-076xlqn.png)

单独int和uint类型大小跟随系统, 64位系统为int64和uint64

‍

unsafe.Sizeof()函数, 查看数据类型的大小

‍

数字字面量语法

v := 0b0010101101,  代表二进制

v := 0o377,  代表八进制

v := ox89u8,  代表十六进制

浮点

float32和float64, 遵循IEEE754, 默认float64

%f, 输出float类型, 

%.2f 输出时保留两位小数

精度丢失问题, 二进制转十进制的通病

类型转换: type()

```go
a := 23
b := float(a)
c := int(a)
```

int和float默认为0

‍

布尔类型, 默认值为false

不允许将整形强制转换为布尔型

布尔型无法参与数值运算, 也无法与其他类型进行转换

string类型默认为空

字符串常用操作

len(str): 求长

+或fmt.Sprintf(): 拼接字符串

```go
str1 := "aaa"
str2 := "bbb"
str3 := fmt.Sprintf("%v %v", str1, str2)
```

strings.Split(): 分割成切片, 需要strings包

```go
var str1 = "123-456-789"
arr := strings.Split(str1, "-")
```

strings.contains: 判断是否包含

string.HasPrefix,strings.HasSuffix: 前缀/后缀判断

strings.Index(), strings.LastIndex(): 字串出现的位置, 及最后一次出现的位置

strings.Join(a[] string, sep string): 把切片链接成字符串

‍

数组

arr := []string{"php", "java", "golang"}

‍

byte字符, 单引号包裹

一个汉字3个字节, 使用utf8, 一个字母一个字节

直接输出byte的时候输出的是这个字符对应的码值

字符需要格式化输出

%c, 表示相应unicode码对应的字符

‍

for 循环

for i := 0; i < len(s); i++ {

	fmt.Print()

}

```go
// rune
for _, r := range(s) {
	fmt.Print()

}
```

修改字符串, 需先转换为[]rune或[]byte, 再转换为string, 两种方式都会重新分配内存并复制字节数组

```go
func changeString() {
	s1 := "big"
	byteS1 := []byte(s1)
	byteS1[0] = 'p'
	fmt.Println(string(byteS1))

	s2 := "白萝卜"
	runeS2 := []rune(s2)
	runeS2 := '红'
	fmt.Println(string(runeS2))
}
```

‍

‍

```go
defer func() {
	Recover()
}		// 只是声明了一个匿名函数


defer func() {
	Recover()
}()    	// 最后的()表示立即调用匿名函数

// 可简化为
defer recover()
```

匿名函数立即被执行, 而Recover的效果被defer

recover()只有在defer的上下文调用时, 才能成功捕获panic

‍

‍
