# C++

三种方式的结合: 

- 过程性语言
- 面向对象语言
- 泛型编程

‍

---

‍

# 一、概述

## 编译

生成可执行文件, 机器语言

```
g++ filename.cpp
g++ -std=c++0x use_auto.cpp
// 或
clang++ filename.cpp
```

## 构建

有系统、有结构的批量编译

### 1. make构建

#### make命令

读入名为makefile的文件, 并执行**第一个**target

```bash
 # 参数
 make -f othername  # 指定文件名, 默认"makefile"或"Makefile"
 make -n         # 编译, 但不会实际运行, 用于调试
```

每次make会检查依赖文件的==时间戳==, 若有更新, 则重新生成目标文件, 否则跳过

```bash
 make    # done.
 make    # make: 'hello' is up to date.
 touch main.c
 make    # done.
```

可指定文件单独编译, 不会make所有

```bash
 make hello
```

#### 编译命令

定义变量

```makefile
 CC = gcc    # 或clang、cl、icc、tcc、wcc、pgcc等
```

#### gcc命令

```bash
 gcc -o <output_file> file   # -o指定生成的文件名, 默认为"./a.out"
 gcc -c file     # 只编译, 不链接, 即生成与file同名的.o文件, 而不生成可执行文件
 gcc -Wall       # 启用警告
 gcc -g          # 生成调试信息
 gcc -02         # 第二级别的优化
```

#### makefile

##### 格式

```makefile
target: dependencies    # 目标文件:  依赖文件
    action              # tab  要执行的命令       # 必须是tab不能是空格
```

两个target的依赖文件和执行命令相同, 可写在同一行, 空格隔开

```makefile
hello world: main.o message.o   # hello和world的依赖文件和命令相同
    gcc main.o message.o -o $@
```

make不显示执行的命令, 在命令前加@符

```makefile
all: hello world
    @echo "all done."   # make后不再显示echo "all done."命令, 只显示"all done."
```

##### 伪目标(.PHONY)

不生成文件的目标, 用来执行一些操作

用.PHONY: 指明操作名, 来区分文件名(若操作名和文件重名, 如clean, 则操作失败)

```makefile
.PHONY: clean all

all: hello world 	# 若all放在第一个, 则make和make all效果相同
	echo "all done." 	# 全部生成后输出all done.

clean: 
	rm -f *.o hello # 删除所有.o文件和可执行文件hello
```

##### 变量

设置变量

```makefile
CFLAGS = -Wall -g -02  # 编译选项, 都赋值给变量
targets = hello world # 所有目标文件
sources = main.c message.c 
objects = main.o message.o
```

自动变量, make自带, 一般以\$开头

```makefile
$@ 	# 表示所有目标文件
$^	# 表示所有依赖文件
$<	# 表示第一个依赖文件
```

使用变量: \$(变量名), 表示这是一个变量

```makefile
gcc $(CFLAGS) $(objects) -o $@
```

##### 通配符

​`%`​: 用于文件名的匹配, 结合自动变量实现自动化构建

```makefile
%.o: %.c  # 每次规则匹配时, $<会被替换为当前规则中唯一相关的第一个依赖文件
	gcc $(CFLAGS) -c $< -o $@
```

​`*`​: 匹配任意数量字符, 不包括/

```makefile
clean:
	rm -f *.o hello world
```

​`?`​: 匹配任意一个字符

##### 函数

​`wildcard`​: 匹配文件名模式, 返回符合模式的文件列表

通常用于获取所有源文件列表

```makefile
$(wildcard pattern)
SRC = $(wildcard *.c)       # 匹配当前目录下的所有 .c 文件
```

​`patsubst`​: 字符串模式替换, 常用于将一组文件的后缀更改为目标文件的后缀

```makefile
$(patsubst pattern,replacement,text)
OBJ = $(patsubst %.c, %.o, $(SRC))  # 将 .c 替换为 .o
```

##### 最终效果

```makefile
# makefile

.PHONY: clean all
CFLAGS = -Wall -g
targets = hello world
sources = main.c message.c
objects = main.o message.o

all: $(targets)
	@echo "all done"

$(targets): $(objects)
	gcc $(CFLAGS) $(objects) -o $@

%.o: %.c
	gcc $(CFLAGS) -c $^ -o $@

clean:
	rm -f *.o $(targets)
```

### 2. cmake构建

跨平台, 根据平台和编译器生成对应的构建文件

配置文件名: CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10) # CMake的最小版本要求
project(HelloWorld) # project命令+工程名
set(SOURCES main.c message.c) # set()命令定义变量, 空格隔开, 可换行写
add_executable(hello ${SOURCES}) # 生成可执行文件, 参数为: 可执行文件名, 依赖文件名
```

使用: 先用cmake命令, 在用make命令

```bash
cd build # 一般新建build文件, 在build文件中cmake
cmake ..
make
```

### 3.task.json构建

使用vs code, command+shift+p, 配置task.json

## 执行

```
 ./a.out    #注意这里的./不可少, 除非路径已在PATH中
```

执行后窗口不自动关闭: 

```
 cin.get();      //有时候需要两行
```

## 程序组织结构

```css
project/
├── src/
│   ├── main.cpp
│   ├── MyClass.cpp
├── include/
│   ├── MyClass.h
├── Makefile (或 CMakeLists.txt)
└── README.md
```

### 预处理

编译之前由预处理器处理

```c++
#include <iostream>  // 包含输入输出流库,使用cin和cout进行输入输出的程序必须包含iostream 
#define MAX_VALUE 100 // 定义一个宏常量
```

### 名称空间namespace

一种命名管理&组织程序的方法

类、函数、变量为C++编译器的标准组件, 都放置在名称空间std中

可单独指定函数为哪个版本: 

```
 using std::cout;
 using std::endl;        //cout和endl使用时不用加std::了
```

可放在函数内或外, 影响范围不同

偷懒写法: 

```
 using namespace std;        //所有names都不用加std::了
```

若放在函数定义之前, 则所有函数都可以使用std中元素

若放在特定函数中, 让该函数能使用std

### 主函数

除了一些框架程序、机器人控制程序以外, 常规的独立程序必须包含一个名为main()的函数

在运行C++程序时, 通常从main()函数开始执行

### 头文件

包含函数声明、类定义、宏定义, 后缀`.h`​ 或 `.hpp`​

通过`#include`​ 将头文件包含

头文件名: 

- 保留老式.h,
- 新式不用任何扩展名, 而用namespace std
- 对于转换后的C, 在文件名前加c, 如cmath

#### 头文件保护符

保证头文件只被包含一次

- ​`#pragma once`​: 简洁、不需要处理宏定义, 更高效, 但不是 C++ 标准的一部分, 不适用于所有编译器
- include guard -- `#ifndef`​:

  ```C++
  #ifndef MY_HEADER_H
  #define MY_HEADER_H

  // 头文件内容

  #endif // MY_HEADER_H
  ```

  可移植性好, 适用于所有标准兼容的编译器, 经广泛验证, 但冗长, 可能出错, 需避免宏名冲突

### 源文件

后缀`.cpp`​

‍

---

‍

# 二、代码规范

## 终止符

分号, 为语句的结束标记, 是语句的组成部分, 不是语句之间的标记, 不能省略

## 注释

所有的程序都以注释开始, 指出源代码的文件名, 并简要总结该程序

```c++
// a C++-sytle comment
// 或者
/* a C-style comment */
```

## 变量名

区分大小写, 以获得更多命名空间

‍

# 三、输入输出

输出

\<\< 符号: 插入运算符(左移运算符重载), 将右侧的字符串发送给cout, 指出了信息流动的路径, 自动根据其后的数据类型调整其行为

cout: 输出流ostream对象, 方法为显示, 接口为\<\<, 可将右侧的信息插入流中

endl: 控制符, 重起一行, cout不会自动移到下一行, 也不会加空格, 每个cout紧接在上一个后面

\\n: 老式换行符, 被视为一个字符, 在字符串引号内最后位置

> endl与"\\n"区别: endl确保程序继续运行前刷新输出, 即将输出立即显示在屏幕上

cin: istream类对象, 可将输入字符转换为接受变量的类型

格式化

回车与空格、制表符作用相同, 统称空白white space

不能把空白放在元素中间, 字符串中间不能有回车(原始字符串可有回车)

标记: 不可分割的元素, 标记之间必须用空白分开(除了括号和逗号)

‍

‍

# 四、关键字

## decltype

C++11 引入, 在编译时推断表达式的类型, 类似于 `auto`​, 但 `decltype`​ 不会实际计算表达式的值, 只是推断其类型

### 常见用途

- **推导变量的类型**
- **函数返回类型的推导, ** 特别是返回类型依赖于函数参数时(`尾置返回类型`​)

- **结合** **​`auto`​**​ , 用于更加复杂的类型推导

### 示例

```c++
int x = 5;
decltype(x) y = 10; // y 的类型是 int
decltype(x + y) z = x + y; // z 的类型也是 int

template <typename T1, typename T2>
auto add(T1 a, T2 b) -> decltype(a + b) {  // 返回值的类型由a+b推导
    return a + b;
}
```

### 与auto的区别

- ​`auto`​ 通过初始化表达式, 即根据初始化的右值推断变量的类型, 忽略表达式的值类别(左值或右值)
- ​`decltype`​ 保留表达式的值类别(左值或右值), 如果表达式是左值, 返回左值引用类型; 如果表达式是右值, 返回普通值类型

```c++
int x = 5;
int& ref = x;

auto a = ref;        // a 是 int, 因为 auto 忽略引用, a 拷贝了 ref 的值
decltype(ref) b = x; // b 是 int&, 因为 decltype 保留了 ref 的引用类型
```

‍

## inline

一个性能优化`建议`​, 即使函数被标记为inline, 编译器也可能不内联

‍

# 五、数据类型

内置类型: 

- 基本类型: 整数、浮点数
- 复合类型: 数组、字符串、指针、结构

### 3.1.2整型

- short
- int
- long
- long long

头文件`<climits>`​定义了符号常量来表示类型的限制(最大值和最小值)

初始化: 声明+赋值

> 可避免忘记赋值的情况发生

可用变量来初始化变量, 条件是: <u>**当程序执行到该声明时, 表达式中的值都是已知的**</u>

独特的初始化方法(C语言没有): 

```
 int foo(432);
```

C++11的初始化方法: 

```
 int bar = {24};
 int baz{24};    //等号可省, 花括号为空时将初始化为0
```

大括号初始化器适用于任何类型, 为通用的初始化语法

无符号类型: 

- unsigned
- signed

整型字面值/常量: 

即显式地书写的常量

- 若第一位为1～9, 则基数为10
- 若第一位为0, 第二位为1～7, 则基数为8
- 若以0x或0X开头, 则基数为16

确定常量的类型: 一般存储为int, 除非使用了特殊后缀或者不能存储为int

cout默认以十进制显示整数, 可用控制符指定: dec(十进制)、hex(十六进制)、oct(八进制)

```
 cout << hex;    //修改显示整数的方式
 cout << waist << endl;
 
 cout << oct;
 cout << weight << endl;
```

> hex、oct均在std中, 要使用using 指令

### 3.1.3char类型

属于整型, cout自动完成转换: 值的类型会引导cout选择如何显示值, 如同样为整形, int类将显示为数字, char类将显示为字符, 不用使用转换函数

成员函数cout.put(), 另一种显示字符的方法, 替代\<\<运算符

显示一个字符

句点--成员运算符

由来: 历史上字符常量被存储为int类型, 所以cout\<\<'M'会显示数字而不是字符M, 

现在已将字符常量存储为char类型而不是int类型

书写字符常量的方式: 单引号括起 -- 清晰, 不需要知道编码方式

转移序列: 作为字符来处理, 用单引号

小技巧: 输出数字时使用endl, 输出字符串时使用'\\n'

可以使用与编码方式相关的数字转移序列, 使用八进制或十六进制, 如ASCII码下的'\\032', 可读性不强, 且依赖编码方式, 无法迁移

一个小技巧: 下划线输入密码

```
 cout << "Enter your password:________\b\b\b\b\b\b\b\b";     //\b实现了退格
 long pwd;
 cin >> pwd;
 cout << "Verified!"
```

通用字符名

unicode, \\u或\\U开头(分别接8、16个16进制位), 可以在字符串中使用

适用于所有系统, 编译器会根据当前的系统, 使用合适的内部编码来表示

字符的符号问题

若用char型变量来存储标准ASCII字符, 则符号无所谓

若char用作数值类型, 则要表示大于127的值时, 应显式设置unsigned char

宽字符类型

wchar\_t, 用于表示扩展字符集, 属于整数类型, 空间足以容纳最大扩展字符集, 长度和符号特征决定于实现

使用wcin和wcout来处理wchaf\_t流

加前缀L来指示宽字符常量/宽字符串

eg: wchaf\_t bob \= L'P';

新增类型: 均为无符号整型(跟随系统)char16\_t: 固定16位, 前缀u

char32\_t: 固定32位, 前缀U

bool类型

将非零值解释为true, 零解释为false

true转换为1, false转换为0

任何数字值和指针值可转换为bool值: bool stop \= 0;

## 3.2限定符

const : 限制声明的含义, 给常量起名, 并限定该名只能表示此常量, 常用来声明数组长度

```
 const type_name = value;    //必须在声明时进行初始化, 若不赋值, 之后无法修改, 值不确定
```

比#define好: 

- 能明确指定类型
- 能将定义限制在指定的函数或文件中
- 可用于更复杂的类型

## 3.3浮点数

范围更大, 但是运算速度比整数慢, 且精度降低

分成两部分存储

E表示法: E或者e, 数字中不能有空格

指数为负: 除以10的乘方

分类: 

- float: 一般为32位
- double: 一般为64位
- long double: 可为80、96、128位

位数限制在头文件`<cfloat>`​或`<float.h>`​

有效位: 数字中有意义的位, 有效位数不依赖于小数点的位置

显示设定: 

```c++
 //不使用E表示法, 使输出使用定点表示法, 显示到小数点后6位
 cout.setf(ios_base::fixed, ios_base::floatfield);   
 //cout一般会删除结尾的0, 但这里不会
 
 //方法二: 
 #include<iomanip>
 std::cout << std::fixed << std::setprecision(2) << num << std::endl;    //可指定小数位数
```

1. \*\*`fixed`​\*\*  
    强制以**定点小数格式**输出（避免科学计数法）。
2. \*\*`setprecision(n)`​\*\*  
    设置小数位数为 `n`​ 位（需与 `fixed`​ 配合使用）。
3. **作用范围**  
    设置后会影响后续所有浮点数的输出格式，直到被修改

临时修改：

```c++
// 局部作用域
{
    ios::fmtflags old_flags = cout.flags(); // 保存原始格式
    cout << fixed << setprecision(1) << 2.718 << " "; // 输出 2.7
    cout.flags(old_flags); // 恢复原始格式
}
cout << 2.718; // 恢复默认输出（可能显示 2.718）
```

浮点常量

float类型: 使用f/F后缀

long double类型: 使用l/L后缀

## 3.4算术运算符

%运算符: 两个操作数必须是**整型**, 若有一个为负数, 则满足: (a/b)\*b + a%b \= a

运算符有结合性

不同类型运算时, C++全部转换为同一类型, 不允许缩窄

赋值运算: 值被转换为接受变量的类型

缩窄: 将大值(如long)赋给小值(如float)将降低精度

浮点转整型时, 采用截取法, 不是四舍五入

列表初始化转换: 不允许缩窄

整型提升(自动转换): 将bool、char、unsigned char、signed char、short转换为int

一般转换为级别高的, 

级别相同, 若有符号可表示无符号的所有可能取值, 则无符号转换为有符号

有符号的级别排列(从高到低): long long、long、int、short、signed char

无符号级别同上, char、signed char、unsigned char级别相同

强制类型转换: 

```
 (type_name) thorn;  //C-style
 type_name(thorn);   //C++-style, 看起来像函数调用  
```

类型转换不改变变量本身, 而是创建一个新的、指定类型的值

强制类型转换运算符: 

```
 static_cast<type_name> (value);     //比传统强制类型转化更严格
```

auto: 自动推断类型, 编译器把变量的类型设置成与初始值相同, 很少使用

## 四、复合类型

## 4.1数组

存储同类型值, 从0开始编号, 不会检查下标是否有效

声明三要素: 

- 元素类型: 不能仅仅声明为数组, 必须为某类型的数组
- 数组名
- 元素个数

元素个数可为表达式, 但值必须已知, 不能为变量

列表初始化: 

```
 int yam[3] = {20, 30};  
 //只有在定义数组时才能初始化, 此后不能再使用
 //提供的值可少于元素总数, 剩下的默认为0, 若{}为空, 所有元素置0
 
 int foo[] {20, 30};   
 //等号可省略
 //若[]为空, 提供几个即为几个元素
```

不能将一个数组赋值给另一个数组

列表初始化禁止缩窄

sizeof用于数组名则得到整个数组大小

## 4.2字符串

C-style字符串: 以空字符结尾(\\0, ascii码为0, 用来标记字符串结尾)

```
 char bird[] = "bubbles";        //引号括起的字符串隐式地包括结尾的空字符, 不用显示地包括
```

C++对字符串长度没有限制, 处理字符串的函数根据空字符的位置, 而不是数组长度来进行处理

"S" 不是字符常量, 而是两个字符S和\\0组成的字符串

字符串常量拼接: 任何两个由空白分割的字符串常量都将自动拼接为一个, 中间紧接, 不额外添加空格

string字符串: 头文件`<cstring>、<string.h>`​

strlen()只计算可见的字符, 返回存储在数组中的字符串的长度, 不把空字符计算在内

cin使用空白确定字符串的结束位置, cin在获取字符数组输入时只读取一个单词, 剩下的留在输入队列中

每次读取一行: 

- getline(), 通过回车键输入的换行符来确定输入结尾, 但**不保存换行符**

  ```
   cin.getline()
       //para1:用来存储输入行的数组名
       //para2:要读取的字符数
  ```
- get(), 不丢弃换行符, 留在输入队列中

  ```
   cin.get()   //不带任何参数的调用可读取下个字符, 即使为换行符
   cin.get(name, ArSize).get();    //链式调用
   //get()比getline()输入更仔细, 使错误检查更简单
  ```

当读取空行, get将设置失效位, 后续输入被阻断: 

```
 cin.clear();    //使用该命令恢复输入
```

混合输入数字和面向行的字符串会导致问题

```
 (cin >> year).get();    //括号中返回cin对象, 可正常工作
```

## 4.3string类

位于名称空间std中, 需要using / std::string

头文件`<string>`​

p83

## 指针

cin混合输入数字和字符

数字: 

```
 (cin >> year).get();
```

字符: 

```
 //旧
 cin.getline(name, length);      //这里的name必须是string类型, 不能是char[]类型
 //新
 getline(cin, name);
```

‍

# 六、变量

声明语句

指出存储类型, 提供位置标签(给存储单元指定名称)

所有变量都必须声明, 先声明, 后使用

赋值语句

可以连续赋值, 从右向左进行

‍

变量赋值语句: 

```
 int braincount;     //告诉程序正在存储整数, 找到一块足够的内存并将该内存标记为braincount
 braincount = 5;    //将5复制到该内存单元中
```

> 程序自己记录内存地址, 可用&运算符检索变量的内存地址

### 3.1.1变量名

命名规则: 

- 只能用字母、数字、下划线
- 第一个字符不能是数字
- 区分大小写
- 不能使用关键字
- 一个下划线开头的名称被保留给实现, 用作全局标识符(名称被声明的位置), 两个下划线/一个下划线+大写字母开头的名称被保留给实现, 使用会导致行为的不确定性
- 没有长度限制, 有的平台有长度限制

推荐的命名方案: 前缀命名, m\_lpctstr前缀

eg: nMyWeight / intMyWeight

m: 类成员, n: 整型, str/sz: 以空字符结束的字符串, b: 布尔值, p: 指针, c: 单个字符

sizeof运算符: 

各个系统的同一类型大小可能不一致, 用sizeof更安全

对类型名使用时应放在括号中, 对变量名使用时可用可不用, eg: sizeof(int), sizeof braincount

## 全局变量和局部变量

global的int有默认初始值, local的没有: 

- ### 全局变量的初始化

  - **全局变量**、**静态变量** 和 **文件作用域变量** 存储在 **全局/静态存储区**(也称为数据段), 这个区域在程序开始运行时由操作系统负责分配, 并在整个程序运行期间存在,
  - 在C++中, 所有未显式初始化的 **全局变量** 会被自动初始化为零值, 这意味着:

	数值类型(如 `int`​、`float`​)会初始化为 `0`​, 

	指针类型会初始化为 `nullptr`​, 

	`bool`​ 类型会初始化为 `false`​, 

	类对象会调用其默认构造函数(如果有的话), 

- ### 局部变量的初始化

  - **局部变量**(即函数内部声明的变量)存储在 **栈区**, 它们的生命周期仅限于函数的执行周期,
  - C++ 中的局部变量**不会**被自动初始化, 如果你没有显式初始化它们, 它们会保留栈内存中的**垃圾值**, 这是未定义行为,

- ### 为什么全局变量有默认初始值, 而局部变量没有？

这与变量的 **存储位置** 和 **内存分配机制** 有关, 

	**全局变量** 存储在 **全局/静态存储区**, 程序启动时由操作系统初始化并分配内存, 由于这个内存区域通常会被清零, 全局变量会自动获得默认初始值, 

	**局部变量** 存储在 **栈区**, 它们的内存是在运行时动态分配的, 编译器不会自动清零栈内存, 栈内存的内容在函数调用和退出时会频繁变化, 因此局部变量在没有显式初始化时会包含栈中的任意数据(也就是所谓的"垃圾值")

‍

‍

# 七、函数

函数

原型: 描述函数接口, 即发送给函数的信息和返回的信息

参数若省略void, 解释为不接受任何参数的隐式声明

不允许函数定义的嵌套, 所有函数的创建平等

函数头为函数与程序其他部分/操作系统之间的接口

## 隐式转换函数

```c++
// 声明格式
operator type();   // type为目标类型
```

### 特性

- 隐式调用
- 无参数和返回类型
- **必须为类的成员函数**

### 注意

- 有潜在歧义性: 涉及多个重载函数时可能导致意外的转换
- 避免滥用: 使代码复杂化, 可读性下降, 通常使用显式转换
- ​`explicit`​关键字: 禁止隐式转换, 只允许显式转换

  ```c++
  double d = static_cast<double>(c);
  ```

‍

‍

# 八、类设计

## 析构函数

类有子类时, 将析构函数声明为`virtual`​,  确保删除派生类对象时可正确调用类和基类的析构函数, 防止资源泄漏

若析构函数无`virtual`​, 则将只调用基类的析构函数, 不调用派生类的, 导致派生类资源未正确释放

‍

‍

## class和struct

区别在于**默认的访问权限: **

- struct成员默认是public
- class成员默认是private

其他方面相似, struct具有完整的面向对象特性, 也有构造函数、析构函数、成员函数、操作符重载等, 有public、protected、provate访问控制, 支持继承和多态

​`惯例`​: 

- struct: 用于仅包含数据而不包含复杂行为的情况, 如简单数据对象、POD类型, 轻量级结构
- class: 用于更复杂的对象和类设计

‍

## 友元

​`friend`​关键字

友元类: 允许一个类访问另一类的私有和保护成员, 实现需要紧密耦合的类, 如封装机制、操作重载、互操作性

```c++
class B; // 先前向声明类 B, 告诉编译器B是一个类

class A {
    friend class B; // 声明 B 是 A 的友元类
private:
    int privateMember;
public:
    A() : privateMember(42) {}
};

class B {
public:
    void showPrivateMember(A& a) {
        // B 可以访问 A 的私有成员, 因为 B 是 A 的友元类
        std::cout << "A's private member: " << a.privateMember << std::endl;
    }
};

int main() {
    A a;
    B b;
    b.showPrivateMember(a); // 输出 A's private member: 42
    return 0;
}
```

‍

‍

# 九、特殊类

## size\_t

​`size_t`​ 一种无符号整型, 表示对象的大小或数组的索引

### 主要特点

1. **无符号类型**: `size_t`​ 无符号, 只表示非负值,非常适合表示内存大小和对象数量
2. **平台相关**: `size_t`​ 占用字节数依赖于编译器和平台, 在32位系统`size_t`​ 为32位, 在64位系统上为64位
3. **定义位置**: `<stddef.h>`​ 或 `<cstdlib>`​(C++)中定义

### 使用场景

1. **数组和指针**: 在处理数组索引和遍历时, 使用 `size_t`​ 可以避免负值索引导致的潜在错误

    ```c++
    size_t length = 10;
    int array[length];
    ```
2. **内存分配**: `malloc`​或`new`​时, 用 `size_t`​ 来指定分配的字节数

    ```C++
    size_t size = 5;
    int* ptr = (int*)malloc(size * sizeof(int));
    ```
3. **标准库函数**: 许多标准库函数的参数和返回值使用 `size_t`​ 类型, 

    如 `strlen`​、`sizeof`​、`std::vector`​ 的 `size()`​ 方法等

### 示例代码

```C++
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec(10); // 创建一个大小为10的vector

    for (size_t i = 0; i < vec.size(); ++i) { // 使用size_t进行索引
        vec[i] = i * 2; // 赋值
    }

    for (size_t i = 0; i < vec.size(); ++i) {
        std::cout << vec[i] << " "; // 输出结果
    }

    return 0;
}
```

‍

‍

## std::map

属于STL, 一个关联容器, 用于存储键值对, 并根据键的顺序自动排列

### 特点

- **有序性**: 根据键的顺序自动排序, 默认使用键的 `<`​ 运算符进行比较
- **唯一性**: 每个键在 `std::map`​ 中是唯一的, 插入一个已存在的键则新的值会替换旧的值
- **基于红黑树**: 通常实现为红黑树, 这使得插入、删除和查找操作的平均时间复杂度为 O($log n$)

### 示例

```c++
// 创建一个 std::map
std::map<int, std::string> myMap;
// 插入元素
myMap[1] = "one";
myMap[2] = "two";
myMap[3] = "three";
// 遍历并输出元素
for (const auto& pair : myMap) {
    std::cout << pair.first << ": " << pair.second << std::endl;
}

// 实现计数
std::map<std::string, int> counter;
std::string words[] = {"apple", "banana", "apple", "orange", "banana", "apple"};
// 计数
for (const auto& word : words) {
    counter[word]++; 	// 若键不存在, 则自动插入该键并初始化为0, 然后递增计数
}
// 输出计数结果
for (const auto& pair : counter) {
    std::cout << pair.first << ": " << pair.second << std::endl;
}
```

## std::unordered_map

一种无序映射, 基于哈希表实现, 查找、插入和删除的平均时间复杂度为 O(1)

### 示例

```c++
std::unordered_map<std::string, int> counter;
std::string words[] = {"apple", "banana", "apple", "orange", "banana", "apple"};

// 计数
for (const auto& word : words) {
    counter[word]++;   // 若键不存在, 则自动插入该键并初始化为0, 然后递增计数
}

// 输出计数结果
for (const auto& pair : counter) {
    std::cout << pair.first << ": " << pair.second << std::endl;
}
```

### std::map 🆚 std::unordered_map:

- map按字母顺序输出
- unordered_map按哈希顺序输出, 性能更好

‍

## std::unique_ptr

独占所有权的智能指针, 同一时间只能有一个unique_ptr指针指向某对象, 指针被销毁时, 所管理的对象会自动被释放

### ​`std::make_unique`​

创建unique_ptr, 简洁安全

#### 特点

- 简洁性: 

  ```c++
  std::unique_ptr<MyClass> ptr1(new MyClass(10));  // 传统方式
  auto ptr2 = std::make_unique<MyClass>(10);       // 使用 make_unique, 不需单独编写new

  auto arr_ptr = std::make_unique<int[]>(5); 		 // 创建一个包含5个整数的动态数组
  arr_ptr[0] = 10; 		// 访问数组元素
  ```
- 异常安全性: 减少内存泄漏的可能, 若对象构造时异常, 可确保内存安全释放
- 减少冗余: 不需重复写对象类型

  ```c++
  std::unique_ptr<MyClass> ptr = std::make_unique<MyClass>();
  auto ptr = std::make_unique<MyClass>(10);
  ```

‍

‍

‍

# 十、特殊机制

## 数组删除器

- **自动内存管理**: 在智能指针的生命周期结束时自动调用 `delete[]`​, 确保动态数组的内存被正确释放
- **与** **​`std::unique_ptr`​**​ **结合使用**: `std::unique_ptr`​ 可接受自定义删除器, 用`std::unique_ptr<T[]>`​ 管理动态数组

```c++
// 使用 std::unique_ptr 和数组删除器管理动态数组
std::unique_ptr<int[]> arr(new int[5]);

// 初始化数组元素
for (size_t i = 0; i < 5; ++i) {
    arr[i] = i * 10;
}

// 输出数组元素
for (size_t i = 0; i < 5; ++i) {
    std::cout << arr[i] << " "; // 输出: 0 10 20 30 40
}
std::cout << std::endl;

// arr 超出作用域时, 自动释放内存
```

# 十一、异常

## 异常安全

指异常发生时, 程序的状态仍保持一致, 且不会导致资源泄漏或数据损坏

### 三个级别(从弱到强)

- **基本保证(Basic Guarantee)** : 

  - 即使抛出异常, 程序中的所有对象都保持在有效的状态(不会发生资源泄漏、对象不一致等问题),
  - 程序可能无法完成原本的操作, 但是它不会造成未定义行为, 系统资源也不会泄漏,
  - 示例: 对象的析构函数不会在抛出异常时导致资源泄漏,
- **强烈保证(Strong Guarantee)** : 

  - 如果异常被抛出, 程序状态将回到异常发生前的状态, 仿佛没有执行过任何操作,
  - 通常, 这意味着操作要么成功并完成所有工作, 要么失败并且不改变任何状态(类似事务中的"原子性"),
  - 示例: `std::vector`​的`push_back`​操作在无法分配内存时会抛出异常, 但原来的容器仍然保持一致,
- **无异常保证(No-Throw Guarantee)** : 

  - 操作承诺不会抛出任何异常, 这种保证通常通过`noexcept`​关键字(在C++11中引入)来实现,
  - 适用于析构函数、移动操作等, 它们通常不应该抛出异常,

### 实现异常安全

- **资源获取即初始化(RAII)** : 使用智能指针(如`std::unique_ptr`​、`std::shared_ptr`​)管理动态资源, 确保在异常发生时资源自动释放,
- **事务性操作**: 在操作完成前, 不要修改对象的状态, 可以通过临时对象保存操作结果, 确保只有在操作成功时才更新对象的状态,
- **异常处理**: 在可能发生异常的代码中, 使用`try`​-`catch`​块来捕获并处理异常, 确保程序状态的完整性,

‍

# 十二、设计模式

## Factory函数

一种创建对象的设计模式, 可结合make_unique()

### 特点

- 封装性: 通过函数**封装对象的创建过程**, 返回一个类的实例, 但不会直接调用构造函数, 使代码更简洁, 更易于维护
- 灵活性: 可扩展, 可根据输入参数或其他条件返回不同类型的对象
- 隐藏复杂性: 如果对象的创建过程比较复杂, 工厂函数可以隐藏这些复杂性, 只暴露一个简单的接口给客户端代码

### 示例

```c++
#include <iostream>
#include <memory>

// 基类
class Product {
public:
    virtual void show() = 0;   // 虚函数show()
    virtual ~Product() {}
};

// 派生类A
class ConcreteProductA : public Product {
public:
    void show() override {
        std::cout << "ConcreteProductA created." << std::endl;
    }
};

// 派生类B
class ConcreteProductB : public Product {
public:
    void show() override {
        std::cout << "ConcreteProductB created." << std::endl;
    }
};

// 工厂函数
std::unique_ptr<Product> createProduct(char type) {   
    if (type == 'A') {
        return std::make_unique<ConcreteProductA>();
    } else if (type == 'B') {
        return std::make_unique<ConcreteProductB>();
    } else {
        return nullptr;
    }
}

int main() {
    auto productA = createProduct('A');
    if (productA) productA->show();

    auto productB = createProduct('B');
    if (productB) productB->show();

    return 0;
}
```

### 应用场景

- **多态对象的创建**: 当需要根据不同条件创建不同子类的对象时, 工厂函数非常有用
- **控制对象的创建**: 工厂函数可以控制对象的创建流程和生命周期管理, 如对象池、引用计数等
- **抽象库或框架接口**: 在设计库或框架时, 工厂函数可以用来提供对象的创建接口, 避免用户直接依赖于具体类的实现

‍

‍

# 十三、泛型编程

### 实例化(Instantiation)

实例化是指编译器根据模板生成具体的类、函数或变量的过程, 当你使用模板时, 编译器会根据你提供的模板参数(类型参数或非类型参数)生成一个具体的实例, 

#### 示例

```
 template <typename T>
 void print(T value) {
     std::cout << value << std::endl;
 }
 
 int main() {
     print(42);       // 实例化 print<int>
     print(3.14);     // 实例化 print<double>
     print("hello");  // 实例化 print<const char*>
     return 0;
 }
```

在这个例子中, `print`​ 是一个模板函数, 当你调用 `print(42)`​ 时, 编译器会生成一个 `print<int>`​ 的实例; 调用 `print(3.14)`​ 时, 生成 `print<double>`​ 的实例; 调用 `print("hello")`​ 时, 生成 `print<const char*>`​ 的实例, 

### 具体化(Specialization)

具体化是指为特定的模板参数提供一个定制的实现, 具体化可以分为显式具体化和部分具体化, 

#### 显式具体化(Explicit Specialization)

显式具体化是指为特定的模板参数提供一个完全定制的实现, 

#### 示例

```
 template <typename T>
 class MyClass {
 public:
     void print() {
         std::cout << "Generic template" << std::endl;
     }
 };
 
 // 显式具体化
 template <>
 class MyClass<int> {
 public:
     void print() {
         std::cout << "Specialized for int" << std::endl;
     }
 };
 
 int main() {
     MyClass<double> obj1;
     obj1.print();  // 输出: Generic template
 
     MyClass<int> obj2;
     obj2.print();  // 输出: Specialized for int
 
     return 0;
 }
```

在这个例子中, `MyClass<int>`​ 被显式具体化, 提供了一个专门针对 `int`​ 类型的实现, 

#### 部分具体化(Partial Specialization)

部分具体化是指为模板参数的一部分提供定制的实现, 部分具体化只能用于类模板, 

#### 示例

```
 template <typename T1, typename T2>
 class MyClass {
 public:
     void print() {
         std::cout << "Generic template" << std::endl;
     }
 };
 
 // 部分具体化
 template <typename T>
 class MyClass<T, int> {
 public:
     void print() {
         std::cout << "Partial specialization for second parameter as int" << std::endl;
     }
 };
 
 int main() {
     MyClass<double, double> obj1;
     obj1.print();  // 输出: Generic template
 
     MyClass<double, int> obj2;
     obj2.print();  // 输出: Partial specialization for second parameter as int
 
     return 0;
 }
```

在这个例子中, `MyClass<T, int>`​ 被部分具体化, 提供了一个专门针对第二个参数为 `int`​ 的实现, 

‍

范型实现bubble_sort:

好的, 下面是一个用 C++ 编写的版本, 满足以下条件: 

1. **使用模板(范型)** , 支持任意类型。
2. **无副作用**, 即不修改原始数据, 而是返回一个新的排序后的数组。
3. **无重复代码**, 支持既可以用于可比较类型(通过 `Comparable`​ 接口), 也可以用于不可比较类型(通过传入自定义的比较函数)。

我们将通过模板重载和类型特征(`std::enable_if`​)来区分可比较和不可比较的情况。

### 代码实现: 

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

// 通用 bubbleSort 函数, 接受一个比较器
template <typename T, typename Comparator>
std::vector<T> bubbleSort(const std::vector<T>& array, Comparator comp) {
    std::vector<T> sortedArray = array;  // 克隆原数组, 避免修改原数组
    size_t n = sortedArray.size();
    for (size_t i = 0; i < n; ++i) {
        bool swapped = false;
        for (size_t j = 0; j < n - 1 - i; ++j) {
            if (comp(sortedArray[j], sortedArray[j + 1])) {
                std::swap(sortedArray[j], sortedArray[j + 1]);
                swapped = true;
            }
        }
        if (!swapped) break;  // 如果没有交换, 则已经排好序
    }
    return sortedArray;
}

// 默认比较器, 用于可比较类型(使用 `operator<`)
template <typename T>
std::vector<T> bubbleSort(const std::vector<T>& array) {
    return bubbleSort(array, std::less<T>());  // 使用标准小于比较
}

int main() {
    // 使用可比较类型(如 int 类型)
    std::vector<int> vec = {5, 2, 9, 1, 5, 6};
    std::vector<int> sortedVec = bubbleSort(vec);  // 使用默认比较器
    for (const auto& num : sortedVec) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    // 使用不可比较类型(如自定义结构体)
    struct Person {
        std::string name;
        int age;
        bool operator<(const Person& other) const {  // 需要定义比较规则
            return age < other.age;
        }
    };
    
    std::vector<Person> people = {{"Alice", 30}, {"Bob", 25}, {"Charlie", 35}};
    auto sortedPeople = bubbleSort(people);  // 使用默认比较器
    for (const auto& person : sortedPeople) {
        std::cout << person.name << " (" << person.age << ") ";
    }
    std::cout << std::endl;

    // 使用自定义比较器(不可比较类型的例子)
    std::vector<Person> people2 = {{"Alice", 30}, {"Bob", 25}, {"Charlie", 35}};
    auto customComparator = [](const Person& a, const Person& b) {
        return a.name < b.name;  // 按名字排序
    };
    auto sortedPeople2 = bubbleSort(people2, customComparator);  // 使用自定义比较器
    for (const auto& person : sortedPeople2) {
        std::cout << person.name << " (" << person.age << ") ";
    }
    std::cout << std::endl;

    return 0;
}
```

### 关键点说明: 

1. **无副作用**: `bubbleSort`​ 函数返回一个新的排序后的数组, 而不会直接修改原始的 `array`​。
2. **适用于可比较和不可比较类型**: 

    - 对于可比较类型(如 `int`​ 或 `Person`​), 我们可以使用默认比较器 `std::less<T>()`​ 来比较元素。
    - 对于不可比较类型, 用户可以传递一个自定义的比较器 `Comparator`​(例如, 按 `name`​ 排序)。
3. **模板函数重载**: 

    - ​`bubbleSort`​ 函数有两个版本: 一个是默认版本(适用于可比较类型), 另一个是接受自定义比较器的版本(适用于任何类型)。
    - ​`std::less<T>`​ 是标准库中的比较函数, 用于执行 `<`​ 操作符比较。

### 流程: 

- **第一部分**: 我们首先定义了一个通用的 `bubbleSort`​ 函数, 它接受一个 `std::vector<T>`​ 和一个比较器 `Comparator`​, 并返回排序后的结果。
- **第二部分**: 为了支持没有显式定义比较操作符的类型, 我们提供了一个重载版本的 `bubbleSort`​, 它使用 `std::less<T>`​ 作为默认比较器, 这样可以直接比较支持 `<`​ 操作符的类型。
- **第三部分**: 在 `main`​ 函数中, 我们展示了如何使用这两个版本的 `bubbleSort`​, 分别对可比较类型(如 `int`​)和自定义结构体(如 `Person`​)进行排序。

### 总结: 

这个 C++ 版本的排序函数使用了模板、函数重载和标准库中的比较器, 避免了重复代码, 支持了可比较和不可比较类型, 并且避免了副作用(通过返回一个新的排序数组)。

‍

现代C++（以C++11及后续标准为基础）的核心现代化体现在**资源管理、抽象能力、性能优化和代码表达力**的提升上。以下是具体特性及其实际应用场景的深度解析：

---

### 🌟 **现代C++的三大革新方向**

|维度|传统C++痛点|现代C++解决方案|典型特性示例|
| ------| ------------------------------| -------------------------| ------------------|
|**资源管理**|手动内存管理易泄漏/悬垂指针|智能指针（`unique_ptr`​, `shared_ptr`​）|自动释放资源|
|**抽象与泛型**|泛型编程依赖宏和模板代码冗余|泛型lambda、`auto`​类型推断|​`auto`​简化类型声明|
|**高性能与并发**|并发模型原始（如pthread）|C++标准库线程支持（`std::thread`​）|基于RAII的锁管理|

---

### 🔥 **现代C++核心特性与应用场景**

#### 1. **智能指针：内存安全的革命**

```cpp
// C++11前：裸指针易泄漏
int* data = new int[100];
// ... 使用后忘记释放 -> 内存泄漏

// C++11后：智能指针自动管理
std::unique_ptr<int[]> data(new int[100]); 
// 数据离开作用域时自动释放（RAII）
```

- **应用场景**：动态内存分配、所有权明确的资源管理
- **优势**：避免内存泄漏，兼容旧代码（如`get()`​获取原始指针）

#### 2. **Lambda表达式：匿名函数的灵活性**

```cpp
// C++11前：需定义函数对象
struct Functor {
    void operator()(int x) { std::cout << x * 2 << std::endl; }
};

Functor f;
f(5);

// C++11后：一行搞定
auto lambda = [](int x) { std::cout << x * 2 << std::endl; };
lambda(5);
```

- **进阶用法**：捕获外部变量、高阶函数

  ```cpp
  int multiplier = 3;
  auto add = [=](int a, int b) { return a + b * multiplier; };
  ```

#### 3. **移动语义：性能的飞跃**

```cpp
class Buffer {
public:
    Buffer(size_t size) { data = new char[size]; }
    // 移动构造函数（C++11）
    Buffer(Buffer&& other) noexcept : data(other.data) {
        other.data = nullptr; // 转移所有权
    }
};

Buffer src(1024);
Buffer dst = std::move(src); // O(1)时间转移，而非O(n)拷贝
```

- **性能对比**：

  - 拷贝构造函数：深拷贝1024字节
  - 移动构造函数：仅指针交换（常数时间）

#### 4. **并发编程：标准库原生支持**

```cpp
#include <thread>
#include <mutex>

std::mutex mtx;

void increment(int& count) {
    mtx.lock();
    count++;
    mtx.unlock();
}

int main() {
    int total = 0;
    std::thread t1(increment, std::ref(total));
    std::thread t2(increment, std::ref(total));
    t1.join();
    t2.join();
    std::cout << "Total: " << total << std::endl; // 输出2
    return 0;
}
```

- **现代改进**：

  - ​`std::lock_guard`​自动管理锁（RAII）
  - ​`std::async`​简化异步任务编程

#### 5. **类型推导与**​**​`auto`​**​**关键字**

```cpp
// C++11前：显式类型声明
std::map<std::string, int> user_scores;

// C++11后：自动推断类型
auto scores = std::map<std::string, int>(); 
scores["Alice"] = 100;
```

- **最佳实践**：

  ```cpp
  // 避免过度使用auto（当类型不明确时）
  auto it = std::find(vec.begin(), vec.end(), value); // 明确it是迭代器类型
  ```

#### 6. **constexpr：编译期计算**

```cpp
constexpr int factorial(int n) {
    return n <= 1 ? 1 : n * factorial(n-1);
}

int main() {
    constexpr int fact5 = factorial(5); // 编译期计算，值为120
    std::cout << "5! = " << fact5 << std::endl;
}
```

- **性能优势**：消除运行时计算开销
- **应用场景**：数组大小、模板参数、常量表达式

#### 7. **范围库（C++20）：迭代器的统一**

```cpp
#include <ranges>

auto numbers = std::views::iota(0, 10);
auto even = numbers | std::views::filter([](int x) { return x % 2 == 0; });
auto squared = even | std::views::transform([](int x) { return x * x; });

for (auto num : squared) {
    std::cout << num << " "; // 输出0 4 16 36 64 100
}
```

- **优势**：链式操作、语法糖简化迭代器代码

#### 8. **模块化（C++20）：头文件的革命**

```cpp
// math.hpp
export module math;
export int add(int a, int b) { return a + b; }

// main.cpp
import math;
int main() {
    std::cout << add(3, 5) << std::endl; // 输出8
}
```

- **改进点**：

  - 编译时间显著减少（模块间只编译一次）
  - 名字空间污染问题缓解

---

### 📈 **性能与开发效率对比**

|特性|传统C++|现代C++|提升幅度|
| ----------------------| --------------| -----------------------| ----------------------------|
|内存分配/释放|手动`new/delete`​|智能指针自动管理|减少50%以上内存泄漏风险|
|对象拷贝|深拷贝开销大|移动语义+拷贝省略优化|提升性能300%（大数据对象）|
|并发线程创建|需手动管理锁|​`std::thread`​+RAII锁|开发速度提升2倍|
|代码行数（复杂功能）|100行|20行|可维护性提高80%|

---

### 🚀 **现代C++的实际项目案例**

1. **高性能游戏引擎**

    - 使用`emplace_back`​代替`push_back`​避免不必要的拷贝
    - 自定义移动语义处理游戏对象资源
    - ​`std::atomic`​实现无锁数据结构
2. **实时金融系统**

    - ​`shared_ptr`​管理跨模块的内存
    - ​`constexpr`​计算固定收益公式
    - 协程（C++20）处理高并发订单流
3. **嵌入式物联网**

    - 移动语义优化传感器数据处理
    - ​`bitset`​紧凑表示设备状态
    - 零开销抽象（ZOA）优化关键路径

---

### 📚 **学习路径建议**

1. **基础阶段**

    - 掌握`auto`​、lambda、智能指针、移动语义
    - 书籍：《Effective Modern C++》Scott Meyers
2. **进阶阶段**

    - 并发编程（`std::thread`​, `mutex`​）
    - 泛型编程与SFINAE
    - C++20模块化与范围库
3. **专家阶段**

    - 模板元编程  
      -协程与异步编程
    - 内存模型与原子操作

---

### 💡 **关键结论**

现代C++的核心价值在于**用零成本抽象（Zero-overhead Abstractions）**  实现高性能与高可维护性的平衡。通过合理运用标准库特性，开发者可以：

- **减少手动内存管理错误**（智能指针）
- **提升代码复用率**（泛型编程）
- **利用编译期优化**（constexpr）
- **简化并发模型**（标准线程库）

建议开发者从C++11开始逐步学习新特性，重点关注**资源管理**和**表达式能力**的提升。对于遗留代码库，可采用渐进式改造策略，优先替换裸指针和手动内存操作。

‍

‍

# `constexpr`​

1. **编译期计算**  
    将运行时计算转移到编译阶段，消除运行时开销
2. **类型安全**  
    在编译期进行严格的类型检查
3. **元编程基础**  
    是模板元编程（TMP）和 SFINAE 的重要补充

### **现代用法（C++17+）**

#### **1. 基础用法**

```cpp
constexpr int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n-1);
}

static_assert(factorial(5) == 120);  // 编译期断言
```

#### **2. 复杂计算**

```cpp
constexpr double geometric_series_sum(double a, double r, int n) {
    double sum = 0;
    for(int i=0; i<n; ++i) sum += a * pow(r, i);
    return sum;
}

constexpr double result = geometric_series_sum(1.0, 0.5, 10);  // 编译期计算
```

#### **3. 编译期分支（C++17** **​`if constexpr`​**​ **）**

```cpp
template<typename T>
constexpr auto get_value() {
    if constexpr (std::is_integral_v<T>) {
        return 42;
    } else if constexpr (std::is_floating_point_v<T>) {
        return 3.14;
    } else {
        return nullptr;
    }
}
```

#### **4. 编译期容器（C++20）**

```cpp
#include <array>

constexpr auto generate_primes() {
    std::array<int, 5> primes{};
    int count = 0;
    for(int i=2; count < primes.size(); ++i) {
        bool is_prime = true;
        for(int j=2; j*j <= i; ++j) {
            if(i % j == 0) {
                is_prime = false;
                break;
            }
        }
        if(is_prime) primes[count++] = i;
    }
    return primes;
}

constexpr auto primes = generate_primes();  // 编译期生成质数数组
```

---

### **四、使用场景**

1. **数学计算**  
    斐波那契数列、阶乘等纯数学运算
2. **类型转换**  
    编译期类型特征检测（结合 `std::is_*`​）
3. **模板元编程**  
    生成类型安全的代码变体
4. **性能优化**  
    替代宏定义，消除运行时计算

---

### **五、注意事项**

1. **限制条件**  
    所有参数和返回值必须是字面值类型
2. **递归深度**  
    编译器可能有递归深度限制（通常 \> 1024）
3. **内存分配**  
    C++20 前禁止动态内存分配，C++20 后允许 `new/delete`​ 但需谨慎
4. **调试困难**  
    编译期错误信息可能非常晦涩

---

### **六、性能对比**

```cpp
#include <chrono>

constexpr int compile_time_fib(int n) {
    return (n <= 1) ? n : compile_time_fib(n-1) + compile_time_fib(n-2);
}

int main() {
    // 编译期计算
    constexpr int ct_result = compile_time_fib(40);  
    
    // 运行时计算
    auto start = std::chrono::high_resolution_clock::now();
    int rt_result = compile_time_fib(40);
    auto end = std::chrono::high_resolution_clock::now();
    
    std::cout << "Compile-time: " << ct_result << "
";
    std::cout << "Run-time: " << rt_result 
              << " (" << std::chrono::duration_cast<std::chrono::microseconds>(end-start).count() << "μs)
";
}
```

输出示例：

```markdown
Compile-time: 102334155
Run-time: 102334155 (1500μs)
```

---

### **七、最佳实践**

1. 优先用于纯函数（无副作用）
2. 结合 `static_assert`​ 进行编译期验证
3. 避免过度复杂的编译期逻辑
4. 使用 `constexpr`​ 变量替代 `#define`​ 宏

通过合理使用 `constexpr`​，可以在保持代码可读性的同时获得极致的性能优化。随着 C++ 标准的演进，它的能力边界正在不断扩展。

‍

std::to_string()

std::stringstream ss;

‍

# map和unordered_map

|特性|​`std::map`​|​`std::unordered_map`​|
| ------| ----------------------| ----------------------------------|
|**底层实现**|**平衡二叉搜索树**（通常是红黑树）|**哈希表**（基于哈希函数 + 链地址法）|
|**元素顺序**|**按键有序**（默认升序）|**无序**|
|**内存占用**|较低（紧凑的树结构）|较高（需要维护哈希表、桶等结构）|

|操作|​`std::map`​（红黑树）|​`std::unordered_map`​（哈希表）|
| ------| ------------------| ----------------------------------------|
|**插入（Insert）**|O(log n)|平均 O(1)，最坏 O(n)（哈希冲突严重时）|
|**查找（Find）**|O(log n)|平均 O(1)，最坏 O(n)|
|**删除（Erase）**|O(log n)|平均 O(1)，最坏 O(n)|
|**遍历（Traverse）**|O(n)（有序遍历）|O(n)（无序遍历）|

---

### **3. 有序性**

- \*\*`std::map`​ **：元素按键自动排序（默认升序，可通过自定义比较函数修改）。** 适用场景\*\*：需要有序遍历、范围查询（如查找某个范围内的键）。
- \*\*`std::unordered_map`​ **：元素无序存储，仅通过哈希表管理。** 适用场景\*\*：仅需快速插入、查找、删除，无需关心顺序。

---

### **4. 自定义键的规则**

- \*\*`std::map`​ **：需要提供** 比较函数\*\*（默认 `std::less<Key>`​，即 `operator<`​）。

  ```cpp
  // 自定义比较函数（例如降序）
  struct CustomCompare {
      bool operator()(const int& a, const int& b) const {
          return a > b;
      }
  };
  std::map<int, int, CustomCompare> myMap;
  ```
- \*\*`std::unordered_map`​ **：需要提供** 哈希函数 **（默认** **​`std::hash<Key>`​** ​ **）和** 相等比较函数\*\*（默认 `operator==`​）。

  ```cpp
  // 自定义哈希函数和相等比较
  struct CustomHash {
      std::size_t operator()(const std::string& key) const {
          // 自定义哈希逻辑
          return std::hash<std::string>()(key);
      }
  };

  struct CustomEqual {
      bool operator()(const std::string& lhs, const std::string& rhs) const {
          return lhs == rhs; // 或者自定义比较逻辑
      }
  };

  std::unordered_map<std::string, int, CustomHash, CustomEqual> myUnorderedMap;
  ```

---

### **5. 内存分配**

- \*\*`std::map`​\*\*：  
  内存分配是连续的（树节点在内存中连续分配），缓存友好。
- \*\*`std::unordered_map`​\*\*：  
  哈希表的桶（buckets）可能分散在内存中，访问时存在跳跃，缓存不友好。

---

### **6. 典型用例**

|场景|推荐容器|
| ----------------------------| ----------|
|需要按键有序遍历|​`std::map`​|
|需要快速查找、插入、删除|​`std::unordered_map`​|
|键是自定义类型且需排序|​`std::map`​|
|键是自定义类型且只需唯一性|​`std::unordered_map`​|

---

### **7. 示例代码对比**

```cpp
#include <iostream>
#include <map>
#include <unordered_map>

int main() {
    // 使用 std::map（有序）
    std::map<int, std::string> orderedMap;
    orderedMap[3] = "three";
    orderedMap[1] = "one";
    orderedMap[2] = "two";
    for (const auto& pair : orderedMap) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }
    // 输出顺序：1 → 2 → 3

    // 使用 std::unordered_map（无序）
    std::unordered_map<int, std::string> unorderedMap;
    unorderedMap[3] = "three";
    unorderedMap[1] = "one";
    unorderedMap[2] = "two";
    for (const auto& pair : unorderedMap) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }
    // 输出顺序不确定（取决于哈希表实现）
}
```

---

### **8. 总结**

|特性|​`std::map`​|​`std::unordered_map`​|
| ------| --------------------| ----------------------------|
|**核心优势**|有序性、范围查询|高效的插入/查找/删除|
|**适用场景**|需要排序、范围操作|追求性能、无需顺序|
|**内存开销**|较低|较高|
|**自定义需求**|提供比较函数|提供哈希函数和相等比较函数|

根据具体需求选择容器：

- 如果需要 **有序性** 或 **范围查询**，选 `std::map`​。
- 如果追求 **性能** 且无需顺序，选 `std::unordered_map`​。

# 新的for写法

|​`for (auto &p : parts)`​|通过 **左值引用** 访问元素|✅ 可以修改原数据|✅ 避免拷贝开销|
| ------| ----------------| -------------------| ---------------------|
|​`for (char c : s)`​|通过 **值传递** 访问元素|❌ 不会修改原数据|⚠️ 可能有拷贝开销|

#### `auto`​ 的类型推导

- ​`auto &p`​：推导为容器元素的左值引用。
- ​`auto p`​：推导为容器元素的副本（值类型）。
- ​`auto &&p`​：万能引用（完美转发，需结合具体上下文）

‍

# std::format

```c++
#include <format>
cout << format("{:<10} {:.2f}", "Price:", 99.999);  // 输出 "Price:    100.00"
```

‍

std::stoi(): 字符串转int

‍

std::unique()：

- 移除容器中 **相邻的重复元素**，保留唯一元素。若未排序，则非相邻重复项无法被去重
- 返回新逻辑结尾的迭代器（指向去重后最后一个有效元素的下一位）。
- **不会改变容器实际大小**，重复元素被移动到容器尾部

容器.erase(start, end)

- **真正缩小容器大小**，仅保留去重后的元素

```c++
pairs.erase(unique(pairs.begin(), pairs.end()), pairs.end());
```

‍

# 结构化绑定

允许你将一个复合类型（如数组、结构体、`std::tuple`​ 等）的成员直接解包到多个独立的变量中，从而简化代码并提高可读性。

---

### **基本语法**

```cpp
auto [var1, var2, ..., varN] = expression;
```

- ​`expression`​ 必须是可分解的复合类型（如数组、结构体、`std::tuple`​ 等）。
- ​`var1, var2, ...`​ 是绑定到复合类型成员的变量。

---

### **使用场景**

#### **1. 解包数组**

```cpp
int arr[3] = {1, 2, 3};
auto [x, y, z] = arr;  // x=1, y=2, z=3
```

#### **2. 解包结构体**

```cpp
struct Point { int x; int y; };
Point p{10, 20};
auto [a, b] = p;       // a=10, b=20
```

#### **3. 解包** **​`std::pair`​**​ **或** **​`std::tuple`​**​

```cpp
std::pair<int, double> p{42, 3.14};
auto [num, val] = p;   // num=42, val=3.14

std::tuple<int, std::string, bool> t{5, "hello", true};
auto [n, s, flag] = t; // n=5, s="hello", flag=true
```

#### **4. 结合范围循环（遍历容器）**

```cpp
std::map<std::string, int> m = {{"a", 1}, {"b", 2}};
for (const auto& [key, value] : m) {
    std::cout << key << ": " << value << std::endl;
}
// 输出：
// a: 1
// b: 2
```

---

### **关键细节**

1. **绑定变量的类型推导**

    - 使用 `auto`​ 时，变量类型由 `expression`​ 的成员类型推导。
    - 可以使用 `const`​ 或引用修饰：

      ```cpp
      const auto& [x, y] = get_point();  // 避免复制
      ```
2. **绑定对象的生命周期**

    - 结构化绑定的变量依赖于原对象生命周期：

      ```cpp
      auto [a, b] = get_point();  // 如果返回临时对象，需确保其生命周期
      ```
3. **支持的复合类型**

    - 数组、结构体（所有成员为 `public`​ 且非静态）、`std::tuple`​ 等满足特定接口的类型。
4. **不可直接修改原对象**

    - 若想通过绑定变量修改原对象，需使用引用：

      ```cpp
      auto& [x, y] = p;  // 修改 x/y 会直接修改 p 的成员
      x = 100;           // p.x 变为 100
      ```

---

### **与传统方式的对比**

#### **传统写法**

```cpp
Point p{10, 20};
int a = p.x;
int b = p.y;
```

#### **结构化绑定**

```cpp
auto [a, b] = p;  // 直接解包，代码更简洁
```

---

### **限制**

- **成员数量必须匹配**：绑定变量数量必须与复合类型的成员数量一致。
- **不支持嵌套解包**：无法直接解包嵌套结构（需分步操作）。
- **仅支持 C++17 及以上标准**。

---

### **总结**

结构化绑定通过简洁的语法将复合类型的成员直接解包到变量中，适用于数组、结构体、元组等场景，是提高代码可读性和编写效率的实用特性。
