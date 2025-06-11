![image](../assets/截屏2025-01-14%2015.08.32-20250114150848-dk60f7u.png)

# Python基础语法

# 一、特殊符号

## 注释符

- 单行: #, 后接一个空格

  - 快捷键(注释/取消注释)command+/
- 多行: 首尾两行三单引号顶格

## 标识符

- 定义规则

  - 数字、字母、下划线组成, 不能由数字开头(此处的字母包含所有国家的语言)
  - 严格区分大小写
  - 不能使用python的保留字、关键字(通过keyword模块中的`keyword.kwlist`​查看关键字)

    ```python
    import keyword
    print(keyword.kwlist)
    ```
- 命名规则

  - 顾名思义命名
  - 下划线命名法, 变量、模块、函数常用
  - 大驼峰命名法, 类名常用
  - 小驼峰命名法
- 删除: `del 变量`​

## 标点符

- 英文文本
- 逗号, 后面加空格
- def、if等后面加冒号:
- 分号;  为语句结束的标记, Python一条语句独占一行, 不建议加分号, 若一行中有多条语句, 语句后必须加分号: 

  ```python
  print(9); print(10)
  ```
- 缩进符, 包含关系, 推荐使用4个空格而不是制表键, 或设置IDE自动将制表键变为4个空格(不同编辑器和环境对制表键的处理可能不同)
- 平级关系, 上下对齐
- 引号: 单引号和双引号用来定义字符串, 功能相同, 引号嵌套时可交替使用避免混乱

  三重引号: `'''`​、`"""`​, 定义多行字符串

  ```python
  text = '''This is a
  multi-line string.'''
  ```

## 运算符

- 算术运算符

  - 不同数据类型不能直接数学运算
  - 分类

    - 一元运算符: 正负号, 正号一般省略, -a就是对a的取反
    - 二元运算符

      +: 加法, 字符串的加法为拼接, 二合一

      -: 减法  \*: 乘法  /: 除法, 结果为`float`​类型 注意做`类型转换`​

      %: 取余  **<u>//: (向下)取整</u>**

      \*\*: 幂, 前底后指, 指数可为正数(结果为int)、 负数、分数(结果为float)
  - 优先级:  `**`​ \> `正负号`​ \> `*、/、//、%`​ \> `加减`​, 用括号控制先后
- 赋值运算符: 

  - 简单赋值运算符\=

    - 前后均有空格, 不需要类型声明
    - 若变量名相同, 后覆盖前
    - 右侧的内存地址传递给左侧
    - > 推荐不使用临时变量交换: a, b = b, a
      >
    - 赋值操作默认是`引用赋值`​, 创建一个新的引用, 即传递原对象内存地址, 实际上未创建新对象

      ```python
      import copy
      a = [0, 1, [2, 3]]
      b = a
      b[2][0] = 99
      print(a)	// [0, 1, [99, 3]]
      print(b)	// [0, 1, [99, 3]] // 简单赋值=传递引用, 指向同一对象
      ```
    - 可一次性赋值多个变量 (相同或一一对应)

      ```python
      x = y = z = 12
      a = b = c = 87, 43, 29
      ```
  - 复合赋值运算符

    - 复合赋值变量必须提前定义, 有初始值, 先运行\=右边的表达式, 再赋值(赋值的优先级最低)
    - `+\=`​、`-\=`​、`\*\=`​、`/\=`​、`%\=`​、`**=`​
  - 赋值表达式: 海象运算符`:=`​, 定义一个变量的同时参与另一变量的定义

    ```python
    y = 2 * ( x:= 7) +1
    ```
- 关系运算符

  - 参与比较的数据需要能进行隐式转换 如: 字符串和整形不可比
  - `>`​、`<`​、`>=`​、`<=`​、`==`​、`!=`​(不等于)

    - `==`​检查两对象的`值`​是否相等，自动处理类型差异, 如`1==1.0`​返回`True`​
  - 结果为布尔值 成立返回True 不成立返回False
  - python支持链式比较, 即 `0 < n < 20`​
  - 字符串比较: 用ASCII码逐一比较, 第一个比不出来则比较第二个, 直到有结果

    常见ACSII码大小规则: 数字 < 大写字母 < 小写字母

    数字0-9递增, 字母a-z递增, 常见: `A`​为65, `a`​为97, `0`​为48
  - 列表比较: 逐一比较元素大小, 与字符串方法雷同
- 逻辑运算符

  - not非: 取反, True返回False, False返回True
  - and且: 全真返回True, 有一假则返回False
  - or或: 有一真则返回True, 全假则返回False
  - 短路原则: 结果可确定则忽略后面, 真或假与, 可替代if嵌套

    - `true`​ or `……`​, 直接输出true, 后面短路
    - `false`​ and `……`​, 直接输出 false, 后面短路
    - 短路求值: `a and b or c`​, **a真则返回b，a假则返回c**
  - 优先级: `not`​ > `and`​ > `or`​
- 成员运算符

  - 用于字符串、元组、列表的判断
  - Num不是容器类型, 不能使用成员运算符
  - `in`​、`not in`​

    > 要善于运用in: if 'L' in name:
    >
- 位运算符

  - 以二进位为单位进行运算, 操作数和结果均为整型

    |运算符|名称|例子|
    | --------| --------| ---------|
    |～|位反|～x|
    |&|位与|x&y|
    |｜|位或|x\|y|
    |^|位异或|x^y|
    |>>|右移|x >> a|
    |<<|左移|x << a|
  - 取反:  公式: ～a \= -1\*(a+1)
  - 位与: 两位均为1, 结果才为1, 否则为0
  - 位或: 两位中只要有一个1, 结果为1, 否则为0
  - 位异或: 两位相反结果为1, 否则为0, 对任一数位异或两次, 结果为本身
  - 右移: 高位用符号位补位
  - 左移: 低位用0补位
- 身份运算符`is`: a is (not) b, 判断两个数据的地址在内存中是否一致
- 三元运算符: `max = a if a > b else b`​

  - `max = a > b? a: b`​是C、Java的三元运算符, Python不支持
- 各运算符优先级: `括号`​>`幂`​>`位反`​>`正负号`​>`乘除余整`​>`加减`​>`位移`​>`位与`​>`位异或`​>`位或`​>`比较`​>`逻辑非`​>`逻辑与`​>`逻辑或`​

## 功能符

- 转义符: \\

  - `\`: 可用作简单换行
  - `\u`: unicode码, 把u后面的4个字符解码, 无则报错
  - `\n`: 换行  `\r`: 回车  `\t`: 水平制表符
  - `\\`: 一个反斜杠\   `\'`: 一个单引号'  `\"`: 一个双引号"
- r符: 去除转义功能, 原样输出
- f符: 配合{}解析格式化输出, 将结果插入字符串中

  ```python
  name = "小红"
  age = 18
  str9 = f'姓名: {name}, 年龄: {age}'
  ```
- b符: 字符串前加b, 表示该字符串类型为字节类型

  ```python
  str1 = 'hello'
  str2 = b'hello'
  print(type(str1))	# class: str
  print(type(str2)) 	# class: bytes
  ```
- %占位符

  - `%s`: 任意类型
  - `%d`: 整型

    - `%0nd`: 对填充的整数指定显式宽度

      - n -- 代表显式宽度
      - 0 -- 不足宽度 左侧填充0 补齐宽度
      - 如学号2, 显示成000002, 占位符写成%06d
  - `%f`: 数字类型, 可以设置格式保留n位小数 `%.nf`​
  - 数值与占位符一一对应, 填充多个值时数据值需要()包含
  - 可以在print函数中一起打印, 也可另起一行赋值

    ```python
    info = '%s的工资信息为: 基本工资%s, 绩效%s, 五险一金%s, 扣税%s, 实发工资%s'
    info = info % (name, bs, perf, issure, tax, ts)
    print(info)
    ```

## 变量

变量存储的是对象的**引用**(在栈中), 实际是一个字符串符号, 用来关联存储在内存中的对象, 赋值语句就是建立变量与对象的映射关系

### 动态类型

变量仅为符号(字符串对象), 本身并不存储类型信息, 类型信息存储在对象上, 两操作数执行运算时必须先检查两个操作数的类型

> Python对象机制的核心: 类型信息和引用计数

`type(a)`: 输出a的类型

### 命名空间

一个**容器**, 存储标识符与对应对象之间的映射关系, 解决命名冲突问题

- 由一个`dict`​实现, 维护(name, obj)关联关系, 可以一个套一个地形成一条**命名空间链**, Python执行时大部分时间消耗在从命名空间链中确定符号对应的对象
- 类、函数、module都对应着一个独立的命名空间
- 一个独立的命名空间会对应一个`PyCodeObject`​对象
- import foo时创建一个新变量foo, 绑定一个PyCodeObject对象, 即foo.py的编译结果
- `dir()`​函数, 查看一个命名空间下的所有名字符号

### 作用域

描述命名空间中标识符的可见性和生命周期, 用于避免作用范围外的代码访问变量

- 分支语句和循环中的变量不存在作用域问题, 外部可直接访问
- `LEGB`​规则: Python使用LEGB顺序来查找一个符号对应的对象

  - `L`: locals, 当前所在命名空间, 函数的参数也属于命名空间内的变量将变量定义在函数内部,

- 修改作用域:

  - `global`: 于函数内修改函数外变量, 可变类型的修改不可添加global

    ```python
    def foo():
    	global a
    	a = 12
    	print(a)
    ```
  - `nonlocal`: 主要用于闭包函数, 于内函数中修改外函数的变量值

    ```python
    nonlocal x
    x += 1
    ```

- `LEGB`​规则: 基于作用域的命名空间查找规则, Python使用LEGB顺序查找符号对应的对象, `locals`​->`enclosing function`​->`globals`​->`builtins`​

  - locals: 局部命名空间, 存储函数或方法内部定义的局部变量、函数的参数, 外部不能访问,

    - 函数内外出现同名变量, 函数内使用函数内变量
    - 若函数中无此变量, 则在全局变量中查找
    - 函数内定义变量名与函数外变量同名, 不改变函数外变量值
    - `locals()`: 查看所有函数内部的局部变量
  - enclosing: 外部嵌套函数的命名空间(闭包中常见), 将变量定义在外函数内, 内函数外
  - globals: 全局命名空间, 存储模块级的变量、函数、类等, 任意位置访问变量, 限于模块内
  - builtins: 内置命名空间, Python启动时自动载入, 如dict、list、type、print, 均位于`__builtins__`​模块中

‍

# 二、数据类型

Python为动态语言, 变量类型不用声明, 由值决定, 不同类型存储机制不同

整型8字节, 字符1字节

序列: 可迭代, 元素有序的容器, 如字节序列bytes、str、list、tuple

引用类型/可变类型: list、dict、set

基本数据类型/不可变类型: int、str、tuple

## 类型操作: 

- type(): 查看类型
- isinstance(x, A_tuple): 判断类型, 是否是指定类型(单类型或类型元组)
- 类型转换: 

  - 隐式转换: 布尔 + 整型 = 整型, 布尔/整型 + 浮点 = 浮点
  - 显式转换: int()、float()、bool()、set()、dict()、str()、list()、tuple()

    - int(x, base = 10), base为进制数, 默认十进制, 浮点数会截断小数部分
    - float(): 不能转换非数字类型
    - bool(): `无参数`​、`0`​、`0.0`​、`空字符串`​、`空列表`​、`空元组`​、`空集合`​、`空字典`​、`None`​返回false
- 数据序列化: 将一数据结构转化为一个字符串或字节流的过程

  - 序列化: 

    - `pickle.dumps(obj)`: 将python对象序列化为一个字节串, 并返回该字节串
    - `json.dump()`: 将**文件**中的对象转化为json字符串, 并**存储到文件中**
    - `json.dumps()`: 将python对象编码成JSON字符串, 默认编码ascii, 中文使用Unicode, 若要显示中文, 参数`ensure_ascii=False`​
  - 反序列化: 

    - `pickle.loads(data)`: 以pickle格式加载字节串(二进制数据)并将其反序列化为python对象
    - `json.load()`: 将**文件**中的内容读取并转化为Python对象
    - `json.loads()`: 将JSON**字符串**转化为Python对象(字典)
  - 文本格式转换问题: 

    - \<html\>文本数据: `txt = res.text`​, 获取文本数据
    - {{{}[]""}}json数据: `js = res.json()`​, 将json转化为程序对象
    - `用户列表 = js['data']['ranks']`​, 路径

## 特殊类型: 

- 空值None: 只有一个值None, 类型为Nonetype
- 可迭代对象: 可逐一返回其成员对象, 可迭代协议`__iter__()`​, 本身不能返回元素, 只是一个容器
- 迭代器: 只能单向取数, 数据取完即**关闭消失**​, 可迭代协议`__iter__()`​、`__next__()`​

  - 迭代器一定是可迭代对象, 可迭代对象不一定是迭代器, 迭代器包含生成器
  - `iter(callable, sentinel)`: 返回一个迭代器

    - callable: 一个可调用的对象, 如函数或lambda
    - sentiel: 停止值, 当callable返回此值时, 迭代结束, 可取代while循环

    ```python
    def reader(s):
    	for chunk in iter(lambda: s.recv(CHUNKSIZE), b''):
    		process_data(data)
    ```
  - list、str、tuple、dict、set不是迭代器, 是可迭代对象
  - 迭代器是惰性对象, 不会一次性加载所有数据, 有自己内部"状态", 会记录当前遍历到的位置, 按需生成下一个元素

  ```python
  numbers = [1, 2, 3, 4]
  iterator = iter(numbers)  # 获取迭代器
  
  # 使用for循环迭代
  for i in iterator:   	# for循环在后台自动用next()处理, 直到触发异常为止, 所以for循环不需next()	
  	print(i)
  
  # 使用 next() 显式获取元素
  print(next(iterator))  # 输出 1
  print(next(iterator))  # 输出 2
  print(next(iterator))  # 输出 3
  print(next(iterator))  # 输出 4
  # 再调用 next() 会抛出 StopIteration 异常
  
  try:
      print(next(iterator))  # 会抛出 StopIteration
  except StopIteration:
      print("Iteration is complete!")
  ```
- 生成器: 特殊的迭代器, 懒加载, 按需产生数据, 同一时间内存中只存在一个数据, 节省内存, 惰性求值适合生成无限序列

  - 获取生成器的方式: 

    - 生成器表达式: 列表推导式的[]变成()
    - 函数+yield(): 暂停函数, 并产生、产出一个数据, 调用不会进入函数, 而是产生一个生成器对象, 当获取生成器中数据时才进入函数, 执行函数中的内容直到yield位置, 暂停函数并把产生的数据返回, 下一次再获取数据, 从上次暂停的位置继续执行到yield位置, 并返回生成的数据
  - `next(gen)`: 获取下一个值

    - `gen.next()`​方法已废弃
  - `gen.send()`: 向生成器内部发送一个值(成为yield表达式的结果)并继续执行生成器的代码

    - 易混淆, `a = yield b`​, yield后的表达式非赋值的值, a的值要等待send()

    ```python
    def calc_avg():
        """流式计算平均值"""
        total, counter = 0, 0
        avg_value = None
        while True:
            value = yield avg_value 	# 生成avg_value后暂停, value接受send()
            total, counter = total + value, counter + 1
            avg_value = total / counter


    gen = calc_avg()
    next(gen)
    print(gen.send(10)) 	# 10被赋值给value
    print(gen.send(20))
    print(gen.send(30))
    ```
  - `gen.throw(exception_type, value=None, traceback=None)`: 向生成器抛出一个异常, 生成器可捕获处理或继续抛出
  - `gen.close()`: 关闭生成器, 触发`GeneratorExit`​异常, 关闭后无法再通过`next()`​获取值
  - `yield from`: 简化生成器委托, 把一个生成器的输出直接传递给外部调用者, 减少编写`for`​循环和手动调用yield

    - `yield from iterable`: 迭代`iterable`​中的每个元素, 并将它们逐个'委托'给外部调用者, Python首先通过iter(iterable)获取迭代器, 通过next()获取值, 通过yield将其传递出去

      ```python
      def generator1():
          yield 1
          yield 2
          yield 3
      
      def generator2():
          yield from generator1()  # 委托给generator1
          yield 4
          yield 5
      
      # 使用生成器
      for value in generator2():
          print(value) 	# 输出1 2 3 4 5
      
      # 指定范围的数字生成器
      def num_generator(m, n):
      	yield from range(m, n + 1)
      ```
    - 可传递子生成器的返回值:

      ```python
      def generator1():
          yield 1
          yield 2
          return "done"  # 生成器返回值
      
      def generator2():
          result = yield from generator1()
          print(f"generator1 returned: {result}")
          yield 3
      
      # 使用生成器
      for value in generator2():
          print(value)
      
      # 输出结果
      # 1
      # 2
      # generator1 returned: done
      # 3
      ```
    - 可将异常传递给委托的生成器:

      ```python
      def generator1():
          try:
              yield 1
              yield 2
          except ValueError:
              print("Handled ValueError in generator1")
      
      def generator2():
          yield from generator1()
      
      gen = generator2()
      next(gen)  # 输出: 1
      next(gen)  # 输出: 2
      gen.throw(ValueError)  # 输出: Handled ValueError in generator1
      ```
  - 常见应用场景:

    - 处理大型数据集(节省内存):

      ```python
      # 逐行读取文件
      def read_large_file(file_path):
          with open(file_path, 'r') as file:
              for line in file:
                  yield line  # 每次返回一行内容
      
      # 使用生成器逐行读取文件, 避免一次性将整个文件加载到内存
      for line in read_large_file('large_file.txt'):
          process(line)  # 逐行处理文件内容
      ```
    - 无限序列生成:

      ```python
      # 生成无限数字序列
      def infinite_counter(start=0):
          count = start
          while True:
              yield count  # 每次返回一个新的数字
              count += 1

      # 生成无限的数字序列
      counter = infinite_counter()
      for _ in range(10):
          print(next(counter))  # 输出 0 1 2 3 4 5 6 7 8 9

      # Fibonacci
      def fibonacci():
      	a, b = 0, 1
      	while True:
      		yield b
      		a, b = b, a + b

      fib = fibonacci()
      ```

    - 生成组合或排列:

      ```python
      # 生成所有排列
      import itertools
      
      def generate_permutations(iterable):
          for item in itertools.permutations(iterable):
              yield item
      
      # 生成所有字符的排列
      for perm in generate_permutations('AB'):
          print(perm)
      ```
    - 管道式数据处理: 在多个处理步骤之间进行数据的传递和处理, 例如, 数据从一个生成器流到下一个生成器, 每个生成器执行某一特定的转换操作

      ```python
      def read_lines(file_path):
          with open(file_path) as f:
              for line in f:
                  yield line.strip()
      
      def filter_lines(lines, keyword):
          for line in lines:
              if keyword in line:
                  yield line
      
      def process_lines(lines):
          for line in lines:
              yield line.upper()
      
      # 使用生成器管道处理文件
      file_lines = read_lines('data.txt')
      filtered_lines = filter_lines(file_lines, 'important')
      processed_lines = process_lines(filtered_lines)
      
      for line in processed_lines:
          print(line)
      ```
    - 批处理数据: 数据需要被分成小批次处理(例如机器学习中的数据加载)

      ```python
      def batch_generator(data, batch_size):
          for i in range(0, len(data), batch_size):
              yield data[i:i + batch_size]
      
      # 假设我们有一个大数据集
      data = [i for i in range(100)]
      
      # 使用生成器按批次处理数据
      for batch in batch_generator(data, 10):
          print(batch)  # 每次输出一个包含 10 个元素的子列表
      ```
    - 延迟计算

      ```python
      def lazy_square(n):
          for i in range(n):
              yield i * i  # 每次返回一个平方值
      
      # 生成一个懒加载的平方数序列
      for value in lazy_square(5):
          print(value)  # 输出 0, 1, 4, 9, 16
      ```
    - 流式数据处理: 如处理网络请求、实时数据流, 允许一边接收数据一边处理数据, 避免在内存中存储大量中间数据

      ```python
      import time
      
      def simulate_data_stream():
          for i in range(10):
              time.sleep(1)  # 模拟延迟
              yield i  # 每秒钟生成一个新的数据
      
      # 实时处理数据流
      for data in simulate_data_stream():
          print(f"Received data: {data}")
      ```

## Num类型

- 整型int

  - 十进制, `int(num, base)`​转换十进制, base参数为原基数(无法转换'80.0' 无法转换'AB' , 可转换int('AB', 16)# 171)
  - 二进制: 0b为前缀, `bin()`​
  - 八进制: 0o为前缀, `oct()`​
  - 十六进制: 0x为前缀, `hex()`​, 不区分大小写, a:10 b:11 c:12 d:13 e:14 f:15

- 布尔类型bool: 只有两个值True和False, T和F**必须大写, ** int子类, 可参与数学运算, 真1假0

- 浮点型float

  - Python只支持**双精度浮点**类型, 且须与本机相关
  - 运算中有浮点数, 则结果是浮点型
  - 科学计数法表示: aen: E/e表示底数10, n为指数, 即10的多少次方

- 复数类型complex: 字母j代替数学中虚数单位i

  ```python
  print(type(1+2j))   # complex
  ```

### 魔法数字

magic number, 在代码中直接使用的、无明确含义的数字/常量, 仿佛凭空出现, 让代码难以理解、维护

```python
if score >= 60:		# 60即为魔法数字
    print("Pass")
else:
    print("Fail")
```

解决:

- 用有意义的常量/枚举替代魔法数字:

  ```python
  PASSING_SCORE = 60
  
  if score >= PASSING_SCORE:
      print("Pass")
  else:
      print("Fail")
  ```
- 在枚举中替换魔法数字: 

  ```python
  from enum import Enum
  
  class Status(Enum):
      SUCCESS = 1
      ERROR = 2
      PENDING = 3
  
  def get_status_message(status):
      if status == Status.SUCCESS:
          return "Success"
      elif status == Status.ERROR:
          return "Error"
      elif status == Status.PENDING:
          return "Pending"
  ```

## Str字符串

文本序列, 有序不可变, 修改只能重新赋值, 长度为1的称字符, 空格也是字符

- 创: 单引号、双引号、三引号、`str()`​
- 查: 

  - 访问

    - 索引从0开始, 负数表示倒数
    - `len()`​获取长度
    - `enumerate()`​列举, 二参为start位置

      ```python
      for k, v in enumerate(str1):		# k为索引, v为值
      	print(k, v, end='*')
      ```
  - 切片: `字符串[开始下标: 结束下标: 步长]`​

    - 开始下标包含, 默认为0, 结束下标不包含, 默认到最后, 步长默认为1
    - 步长为正, 起始索引在结束索引左边;  步长为负, 从右向左取值, 起始索引在结束索引右边
    - `str[::]`​原样输出, `str[::-1]`​镜像输出
  - 查找

    - `count()`: 统计子串在指定字符串中出现次数, 不重叠, 如在abababab中找abab, 只有两个

      `count("欲统计内容", 开始位置, 结束位置)`​ 开始、结束位置为可选参数
    - `find()`: 查找指定字符第一次出现位置下标, 无则返回-1
    - `rfind()`: 查找指定字符追后一次出现位置下标, 无则返回-1
    - `index()`: 类似find()函数, 无则报错

      ```python
      if 'xxx' in word:
      	word = word[:word.index(' ')]
      ```
    - `rindex()`: 类似rfind, 无则报错
    - `max()`​、`min()`: 按ASCII码值比较大小, 逐个比较
  - 判断

    - 英语&大小写

      - isupper( ): 检查字母是否全部大写;  其他字符不管, 只检查英语
      - islower( ): 检查字母是否全部小写;  其他字符不管, 只检查英语
      - istitle( ): 检查字符串中单词的首字母是否大写, 其他小写
      - 成员运算符方法 `if i in string.xxx`​

        - 大写: `string.ascii_uppercase`​
        - 小写: `string.ascii_lowercase`​
        - 所有英文字母: `string.ascii_letters`​
    - 首尾

      - `startswith(str/元组)`: 是否以指定字符集中任一字符开头
      - `endswith(str/元组)`: 是否以指定字符集中任一字符结尾
    - `isdigit()`​/`isnumeric()`: 检查是否全部为数字;  

      成员运算符方法: `in string.digits`​
    - `isalpha()`: 检查字符串是否全部为字母(各国)

      - 汉字: `if '\u4e00' <= a <= '\u9fa5'`​
      - 编码方法: `s.encode().isalpha()`: 纯英文字母
    - `isalnum()`: 是否是数字或字母(各国);  纯数字/纯字母/数字和字母

      编码方法: `s.encode().isalnum()`: 数字或英文字母
    - `isascii()`: 是否是ascii中的符号
    - `isspace()`: 判断文本中是否包含空白字符

- 改: 

  - 大小写转换

    - 小转大: `upper()`​
    - 大转小: `lower()`​
    - 大小互换: `swapcase()`​
    - 每个单词首字母大写并且其他小写: `tittle()`​;  用不连续来区分单词
    - 第一个单词首字母大写并且其它全部小写`.capitalize()`​
  - 分割

    - `split( sep=None, maxsplit=-1 )`​ 以指定字符sep分割字符串, 默认以空白分割, 返回列表

      - \\t\\n\\r\\v\\f都是空白
      - maxsplit如省略, 则不限制分隔次数
    - `rsplit()`: 从右侧分割
    - `splitlines()`: 按行分割字符串,输出为['xxxxx', 'xxxxx', 'xxxxx']
  - 拼接

    - `+`: eg: `"12" + "13" = "1213"`​
    - `join()`: 以指定字符合并字符串, 被拼接的数据只能是字符串类型的

      ```python
      list1 = ["1", "2", "3", "4", "5"]
      pf = "-"
      print(pf.join(list1)) 		# 1-2-3-4-5
      print('-'.join(list1))
      ```

      - > 推荐使用序列构建字符串: name = ''.join(char_list)
        >
  - 填充

    - `.center(n)`: 将字符串填充到指定长度, 默认空格填充, 原内容居中, 二参str指定填充符

      - print(str1.center(50))         # 填充到50个长度
      - print(str1.center(50,"\*"))     # 用\*填充到50个长度
    - `.ljust()`: 左对齐, 右侧填充, 默认空格
    - `.rjust()`: 右对齐, 左侧填充, 默认空格
    - `.zjust()`: 零填充, 0在左
  - 替换: `replace('旧', '新', 次数)`​, 无次数则全部替换
  - 编解码

    - 编码和解码的方式要一致, 否则报错/乱码
    - ascii

      - 对ascii中原有符号, 编码前后内容不变;  其他编码, 内容前有`\`​
      - `ord()`: 获取对应十进制数据
      - `chr()`: 获取对应字符
    - utf-8(一个汉字3个字节) gbk(一个汉字两个字节)

    - `encode(字符集)`: 以指定字符集编码
    - `decode(字符集)`: 以指定字符集解码
- 删: `strip()`​, 去除字符串两边指定字符, 默认空格

  - 不是整个进行检验, 而是一个字符一个字符的判断 遇到不匹配的字符就停止, 左边的从左到右去除, 右边的从右到左移除
  - `lstrip()`: 去除左边指定字符, 默认空格
  - `rstrip()`: 去除右边指定字符, 默认空格

## List列表

有序可变, 类型可不同, 可嵌套, 可变: 内存地址不变, 数据可变

变量实际为指向堆中元素的指针, 若元素为列表, 则堆中存储的是列表的指针, 故列表类型变量赋值时, 指向堆中同一地址; 浅复制时, 只复制基本类型, 元素为指针的, 实际上仍为同一元素; 深复制时, 复制所有元素拷贝

- 创

  - `[]`​包含
  - `list()`: 参数必须为可迭代对象
  - `copy()`: 复制, 生成新列表
  - 切片复制: list1[::-1], 也生成新列表
  - 列表推导式: 

    - `[目标值 for i in range() if 条件]`​
    - `list(目标值 for i in range() if 条件)`​
- 查

  - 访问: 下标, 从0开始, -1表示最后一个
  - 切片: `列表名[开始下标: 结束下标]`​
  - `count()`: 统计次数
  - `index()`: 返回元素的索引
  - 成员`in`​/`not in`​
  - 迭代用enumerate: 

    ```python
    fruits = ['orange', 'grape', 'pitaya', 'blueberry']
    for index, fruit in enumerate(fruits):
    	print(index, ':', fruit)
    ```
- 改

  - `reverse()`: 翻转元素顺序, 改变原列表
  - `sort()`: 对元素排序, 默认升序, 改变原列表

    降序: `list1.sort( reverse=True )`​

    - 注意与sorted的区分: sort只是list类型的方法, sorted是高阶函数, 可对任意序列排序 sort改变原数据, sorted生成新数据
    - 注意有负数的情况, 必要时使用abs()
  - `insert(下标, 内容)`​ 此时下标不能为负, eg: list1.insert(2, 'a')
  - `append()`: 尾部追加单个元素

    - 也可使用运算符+/+\=, 但单个元素必须用[]包含
    - 若append列表, 则将列表作为一个元素追加
  - `extend(序列)`: 添加多个元素, 必须为序列
  - +或+\=: 合并
  - \*或\*\=: 重复, 右操作数为次数
  - 修改切片

    - 切片连续: `list[-3:] = [98, 99, 100]`​ 右侧必须是可迭代数据
    - 切片不连续: 需要新赋值和获取数量一致
- 删

  - `pop(下标)`: 删除指定位置的元素, 默认为最后一个元素, 返回原列表
  - `remove(元素)`: 若有重复, 只能删除找到的第一个

    - 易错点: 因为列表的可变性, 在遍历的同时删除, 原列表发生变化, 继续遍历会漏掉一些元素

      解决: `for i in list[:]或list.copy()`​ 从镜像中遍历, 从原表中删除
  - `clear()`: 清空列表, 保留空列表

## Tuple元组

有序不可变, 可嵌套元组, 占内存比列表少

- 创

  - `tuple()`​
  - `()`​包含

    - 只有一个元素时, 必须加一个逗号表示元组, 若不加逗号, 则根据元素的类型决定数据类型
    - 有时小括号可省略
  - 星号表达式

    - 装包

      - ```python
        b = 100, '张三'
        print(b) 		# (100, '张三')
        ```
      - 含有`*args`​的情况: 按顺序每个键都要有值,  多余的给\*

        eg: \*a, b, c, d: bcd分别拿到参数列表中后三个, 其他的打包成列表给\*a, \*b或\*c或\*d原理相同
    - 拆包

      - ```python
        a, b = (100, '张三')
        print(a) 	# 100
        ```
      -  拆包可迭代对象, 相当于遍历
      - 函数要求独立参数, 调用函数时用\*拆包实参列表(拆包字典用**)

        ```python
        args = [3, 6]
        list1 = list(range(*args))
        def func(a, b, *args):	# 省略定义
        func(*[1, 2, 3, 4])		# 调用时, 用*拆包参数列表
        ```
- 查

  - 访问: 下标0为首, -1表最后
  - 切片: `元组名[开始下标: 结束下标]`​
  - 判断: `in`​/`not in`​
  - 比较: 相同位置逐个比较
- 改: 合并: +, 重复: \*
- 删: del 元组: 删除元组, 释放内存, 再次引用会报错

## Dict字典

底层为高度优化的哈希表结构，有序（官方保持插入顺序），无链表，使用开放寻址（线性探测）

- 内部结构：

  - `ma_keys`​：存储键keys和哈希值
  - `ma_values`​：存储值values
  - `entries`​数组：用于查找和维护key的索引信息
- 核心思想：引入新数据结构`compact dict`​，使用两个数组，一个保存key-hash-value对，一个保存索引顺序（即插入顺序）
- 相比`OrderedDict`​：dict适合常规使用，性能好，OrderedDict支持更多功能（如移动元素）

‍

- 创

  - `{}`​, key和value组成

    - {key1: value1, key2: value2, ……}中间逗号隔开
    - 键不能重复, 重复后覆盖前, 键的数据类型为不可变的类型, 如不能为列表
  - `dict()`​

    - `dict(键1:值1, 键2:值2……)`​
    - `dict(变量1=值1,  变量2=值2……)`​,此处变量名不可加引号

    - `dict([(键, 值), (键, 值), (键, 值)])`​
    - `dict(zip( [*键], [*值] ))`​, 键值数量不一致时, 以少的为准
  - 字典推导式: 

    - `{ k: v for k, v in 字典.items( ) if 条件 }`​, 此处必须放键值对
    - `dict((k, v) for k, v in 字典 if 条件 )`​, 此处不能放键值对
    - 建议先把键定义出来
  - `dict.fromkeys(序列, 自定义值)`: 根据键快速制作一个字典, 值默认为None, 也可自定义
  - `copy()`: 复制
- 查

  - 访问值: 根据键获取值, 成员运算针对键, 不能用下标/值定位元素

    - `字典名[键]`​, 访问不存在的key会报错
    - `字典.get(键, 不存在时返回值)`​, 不存在时返回值默认为None
  - `values()`: 获取所有value
  - `keys()`: 获取所有key
  - `items()`: 获取所有键值对
  - `len()`: 获取字典元素的个数
  - 遍历

    - `for i in dict:`​, 获取的仅为键
    - `for v in dict.values( ):`​, 获取的仅为值
    - `for k, v in enumerate(dict):`​, 返回索引和键, 而不是键值对
    - `for k, v in dict.items( ):`​, 返回元组形式
  - 词频统计: 

    ```python
    from collections import Counter
    print (Counter(str1))
    ```
- 改

  - 增: `字典名[键] = 值`​, (原字典中无该键)
  - 改: `字典名[键] = 值`​, (原字典中有该键)
  - 增改: `setdefault(键, 值)`​, 有则不动, 无则增加
  - 合并: `update(序列)`​, 可为字典、键值对、变量\=值、元组/二维数据、zip()
- 删

  - `del 字典[键]`​
  - `pop(键)`​
  - `popitem()`: 默认删除最后一个元素
  - `clear()`: 清空字典, 保留字典结构

## Set集合

唯一性, 元素不可重复, set(序列)即可去重

无序性, 无索引, 内部使用哈希表存储元素, 排列顺序与添加顺序无关

可变性, 增减元素, 内存地址不变

- 创

  - `set()`​
  - `{}`​包含, 不能构建空集合, 空集合为`set()`​
  - `copy()`: 复制(copy函数可用于列表、字典、集合等可变类型)
  - 集合推导式
- 查

  - `len()`: 获取集合长度
  - 成员运算: `in`​/`not in`​
  - 比较: 返回布尔值

    - 易错: 集合的比较不同于其他类型, **不是逐一比较**
    - \>: 包含, 超集
    - \<: 被包含, 真子集
    - \<\=: 子集, 不一定是真子集
    - \>\=: 包含, 不一定真包含
    - \=\=、!\=
  - 判断

    - `isdisjoint()`: 是否无交集, 无交集返回True
    - `s1.issubset(s2)`: s1是否被s2包含, 可以全等
    - `s1.issuperset(s2)`: s1是否包含s2, 可以全等
- 改

  - 增

    - `add()`: 添加单个元素, 若已存在会报错
    - `update()`: 以列表形式添加多个元素
  - 算

    - 交集: `a & b`​等同于`a.intersection(b)`​

      `a &= b`​等同于`a.intersection_update(b)`: 改变原集合, 把交集赋值给a
    - 并集: `a | b`​ 等同于`a.union(b)`​

      `a |= b`: 并集并改变原集合
    - 差集: `a - b`​等同于`a.difference(b)`​, 不满足交换律

      `a -= b`: 差集并改变原集合
    - 对称差集: `a ^ b`​等同于`a.symmertric_difference(b)`​, 并集 - 交集

      `a ^= b`​等同于`a.symmertric_difference_update(b)`​, 改变原集合, 把对称差集赋值给a
    - 非: ～
- 删

  - `pop()`: 随机删除一个元素
  - `remove()`: 删除指定元素,不存在会报错
  - `discard()`: 删除指定元素, 不存在不报错
  - `clear()`: 清空集合, 保留结构

‍

---

‍

# 三、流程控制

- 顺序结构: 从头到尾, 不重复, 不跳过

  - pass关键字: 无实际意义, 占位, 保证语法完整, 不输出
- 选择(分支)结构

  - 单分支语句: `if`​, 注意if句后的冒号 表达式不成立时, 不执行语句
  - 双分支语句: `if else`​

    - 使用三元运算符简化: `max = a if a > b else b`​
  - 多分支语句: `if elif else`​从上往下匹配条件, 否则执行else语句
  - 分支嵌套
- 循环结构

  - while: 初始表达式只执行一次, 条件不满足时, 跳出整个循环

    - while也可以有else: 若执行break则不执行else, 未执行break则执行else
  - for循环:

    - i不使用时可用`for _ in ...`​
    - for-in-else循环: 循环正常结束时执行else

  - break: 用于跳出整个循环, 即结束while
  - continue: 跳出当前循环, 不执行更新条件, 执行下一个while
  - 死循环: 一般用while循环实现: `while True`​或`while 1`​
  - 循环嵌套: 一般循环嵌套不超过三层, 双重循环中, 一般外层控制行数, 内层控制列数

‍

---

‍

# 四、函数

一等公民, 封装功能, 按需调用, 可嵌套, 简洁易维护, 属于`function类`​

查看函数源码: comand+点击功能名称

先定义后调用, 否则报错

## 一等公民

指可作为函数参数、函数返回值、赋值给变量、存储在数据结构中、传递和操作的对象, 即可像任何其他类型的数据一样被使用和操作的实体, Python中的'一等公民':

- 函数
- 类

  ```python
  class Dog:
  	pass
  
  Animal = Dog
  dog_instance = Animal("Max")
  ```
- 方法

  ```python
  class Cat:
      def __init__(self, name):
          self.name = name
  
      def meow(self):
          return f"{self.name} says meow!"
  
  cat = Cat("Whiskers")
  meow_method = cat.meow  # 获取方法
  print(meow_method())  # 输出: Whiskers says meow!
  ```
- 对象

## 语法

```python
def 函数名():
	函数体
	[return 返回值]

# 带元信息
def 函数名(params: type) -> type: 		# type指明参数类型和返回值类型
	'''
	comment						# 写函数含义注释
	:param参数名:				# 写参数释义, 可有多行
	:return:					# 写返回值释义
	'''
	函数体
	[return 返回值]

# 可像变量一样赋值后调用
def myprint():
	pass

t = myprint
t() 	# 调用t()

# getattr返回md5函数对象, 最后的括号则调用md5()
self.hasher = getattr(__import__('hashlib'), alg.lower())()
```

- 函数名遵循标识符命名规则, 尽量顾名思义, 若重名, 则后覆盖前
- 参数: `()`​为参数列表, 可为空

  - 参数类型

    - 必须参数/位置参数: 调用时必须以正确顺序传参, 形实数量一致, 否则报错
    - 关键字参数: 允许形实乱序, 自动匹配
    - 默认参数: 定义时给形参的默认值, 无实参则使用默认值, 有实参则覆盖默认值

      - 参数列表中若有默认参数, 应放在最后, 否则报错
    - 不定长参数(可变参数): 必须放在普通参数之后

      - \*args: 接收多个位置参数, 得到参数为元组
      - \*\*kwargs: 接收多个关键字参数, 得到参数为字典形式, 传入时使用键值对形式(k \= v)
    - 命名式关键字参数: 限制只能使用关键字方式传参, 若形参中有\*, <u>*后面的必须用关键字参数传参</u>, 而不能用位置传参, 调用函数时必须明确参数名

      ```python
      def greet(name, age, *, country='China'):
      	pass
      
      greet('Alice', 30, country='USA') 	# *后的参数必须用关键字传参
      ```
  - 拆包实参列表

    - 函数要求独立参数, 参数不独立时, 使用\*操作符把参数从**列表、元组、字典**中拆包

      ```python
      func(*[1, 2, 3]) 	# 参数在列表中, 用*拆包
      func(**dict_example)
      ```
- 函数体: 要实现的功能
- 返回值: 

  - return a \=\= b, 返回布尔值, 简单明了

  - 可一次返回多个数据, 以元组形式保存
  - 若出现return, return后面的程序(同层级)不会执行
  - 若return后无任何数据, 默认返回None, 若不写return, 等价于return None
  - 可用一个变量接受return的数据

## 原理

`def`​实际上是执行一条指令, 用来创建函数, 函数是执行到的时候才创建, 先创建一个**函数对象**, 然后将func这个名称符号绑定到这个函数上

```python
# def func()的字节码
30 LOAD_CONST               5 (<code object func at 00D97650, file "demo.py", line 6>)
33 MAKE_FUNCTION            0
36 STORE_NAME               2 (func)
```

Python无法实现C和Java中的重载, 用def语句再次创建一个同名的函数时, 这个变量名就绑定到新的函数对象上

## 内置函数

内部设置, 直接使用

- `input()`: 接收类型为str, 程序在input函数暂停等待, 输入并回车后继续
- `eval()`: 解析字符串, 转变成数据对应的类型, 如将字符串'10'转变为int类型10

  - 应尽量避免使用，会执行任意传入的字符串作为代码, 且执行效率低于静态代码

    ```python
    user_input = "__import__('os').system('rm -rf /')"
    eval(user_input)
    ```
  - 替代：`ast.literal_eval()`​，仅支持字面量，如数字、字符串、列表、字典等，不执行代码
- `print()`: 参数: 

  - `self`: 类名, 一般指当前类
  - `*args`: 可变参数, 可以是多个, 表示要输出的数据
  - `sep`: 输出多个数据时数据的间隔, 默认为空格
  - `end`: 输出完毕后的结尾符号, 默认为\\n(技巧: 利用`print()`​换行)
  - `file`: 表示文件名称, 默认为控制台
- `range([start], end, [step])`​

  - `start`: 开始值, 包含, 可选, 默认为0
  - `end`: 结束值, **不包含**, 可选
  - `step`: 步长值, 两值间隔, 默认为1

  - 不能直接print, 须借助`list()`​函数 (将其他类型数据转换为列表)
- `format()`: 格式化函数, `f'{}'`​更简单

  - 不设定指定位置, 按照默认位置: 

    ```python
    print('{} {} {}'.format('i', 'am', 'Groot'))
    ```
  - 设定指定位置(下标): 

    ```python
    print('{1} {2} {0}'.format('Groot', 'i', 'am'))
    ```

    ![截屏2024-12-25 14.33.57](../assets/截屏2024-12-25%2014.33.57-20241225143400-bjpm5i3.png)​
  - 直接设定参数: 

    ```python
    print('{Subject} {Verb} {Object}'.format(Subject='i', Verb='am', Object='Groot'))
    ```
  - 通过字典设定参数: 

    ```python
    dir_case = {'Subject':'i', 'Verb':'am', 'Object':'Groot'} 
    print('{Subject} {Verb} {Object}'.format(**dir_case))
    ```
  - 通过列表设定参数: 

    ```python
    list_case1 = ['i', 'am'] 
    list_case2 = ['Groot'] 
    print('Subject:{0[0]} Verb:{0[1]} Object:{1[0]}'.format(list_case1, list_case2))
    ```
  - 通过对象设定参数: 

    ```python
    class setValue(object): 
    	def __init__(self, value):
    		self.value = value  
    
    my_value1 = setValue('i am Groot') 
    print('value1是: {0.value}'.format(my_value1))
    ```
  - 格式化控制符: 

    语法: `{参数序号/参数名: [填充符][对齐方式][宽度]}`​

    ![截屏2024-12-25 14.35.44](../assets/截屏2024-12-25%2014.35.44-20241225143547-v7jo88j.png)​

    ```python
    print('数字为: {:.3f}'.format(3.1415))
    ```

    ![截屏2024-12-25 14.27.11](../assets/截屏2024-12-25%2014.27.11-20241225142714-ycg5k8w.png)

    - `^`​, `<`​, `>`​分别是居中、左对齐、右对齐, 后面带宽度
    - `:`​修饰符, 后面带填充的字符, 只能是一个字符, 不指定则默认空格填充
    - `+`​表示在正数前显示+, 负数前显示-
    - `空格`​表示在正数前加空格
    - b、d、o、x、X: 二进制、十进制、八进制、十六进制(小写)、十六进制(大写)
    - s: 字符串
    - f/F: 十进制浮点数, g/G: 十进制整数或浮点数, e/E: 科学计数法表示浮点数
  - 格式化指令: 

    - `!s`: 调用值的`__str()__`​, 将值转为字符串

      ```python
      print('Value: {!s}'.format(value))
      # 也可用
      print(f'Value: {value!r}')
      ```
    - `!r`: 调用值的`__repr()__`​, 将值转为'官方字符串表示形式'

      ```python
      print('Value: {!r}'.format(value))
      ```
    - `!a`: 调用值的`__ascii()__`​, 将值转为ASCII编码的字符串表示形式

      ```python
      print('Value: {!a}'.format(value))
      ```
  - 采用{}转义: 

    ```python
    print ("{} 是 {{银护的台词}}".format("i am Groot")) 	# i am Groot 是 {银护的台词}
    ```
- `enumerete()`: 枚举函数
- `abs()`: 求绝对值
- `pow(base, exp, mod)`: 求幂

## 匿名函数

定义时不使用def关键字, 而是用`lambda`​表达式, 调用时一般使用变量接收

简单清晰, 一般只有一行, 不能包含多条语句

结果返回给`lambda()`​, 不用return

可没有参数, 仅作为函数的'调用器': `lambda: stream.read(self.size)`​

```python
lambda 参数列表: lambda体  		# 参数列表不用小括号

test = lambda x, y: x * y 
print(test(12, 4))

# 基于递归和欧几里得算法找到x和y的最大公约数
gcd = lambda x, y: gcd(y, x) if x > y else (y % x and gcd(y % x, x) or x)

# 结合__import__函数计算阶乘(不推荐)
fac = lambda x: __import__('functools').reduce(int.__mul__, range(1, x + 1), 1)
# __import__('module_name')动态加载模块, 不是语句, 是函数
# int.__mul__: Python乘法运算符*的底层实现

# lambda仅调用函数
for buf in iter(lambda: stream.read(self.size), b''):
# 每次迭代都调用lambda, 并将结果和b''比较(表示数据流读取完毕), True则停止迭代
```

## 回调函数

把函数1作为参数传到函数2中, 函数1即为回调函数, 常用于高阶函数

- `map(func, iterable)`: 映射

  - func为自定义操作函数, 返回变换之后的元素
  - iterable为容器型数据
- `filter(func/None, iterable)`: 过滤

  - func为过滤条件函数, 返回布尔值
  - None为不设置任何函数处理, 仅布尔取值, 过滤了为False的数据
  - iterable为容器型数据
- `functools.reduce(func, sequence)`​

  - 累计汇总, 对序列中的数据按指定功能进行汇总操作
  - 需要`import functools`​

    ```python
    reduce(lambda x, y: x+y, [1, 2, 3, 4, 5])
    ```
- `max(容器, key=函数)`​
- `min(容器, key=函数)`​
- `sorted(序列, key, reverse)`: 排序,  默认升序, 返回新列表, 不改变原数据

  - 降序: `list3 = sorted(list1, reverse=True)`​
  - `key=排序规则`: 可传递函数, 如`len()`​或者lambda等

## 闭包closure

指**一个函数**和**其周围的自由变量的引用**组合而成的对象, 可让函数'记住'它定义时的环境, 即使函数被调用时这些环境已离开作用域

### 特点

- 函数嵌套函数
- 自由变量: 内层函数引用了外层函数中的变量
- 持久化环境: 即使外层函数执行完毕, 闭包仍然保留对自由变量的访问权

### 结构

- **外层函数**: 提供了定义和变量的作用域
- **内层函数**: 引用了外层函数中的变量, 并作为返回值存在

```python
# 一个简单的闭包
# 定义了一个变量x, 并返回inner_function
def outer_function(x):
	# 使用了外层函数的变量x
    def inner_function(y):
        return x + y  # 内层函数引用了外层函数的变量 x
    return inner_function

# 创建一个闭包
closure = outer_function(10) 	# 返回inner_function, 并记住x=10

# 调用闭包
print(closure(5))  # 输出 15

# 用闭包保存状态, 可创建带有记忆功能的函数
def make_counter():
    count = 0  # 外层变量

    def counter():
        nonlocal count  # 修改外层变量
        count += 1
        return count

    return counter

# 创建计数器
counter1 = make_counter() 	# 每个make_counter调用都会创建一个新的闭包
counter2 = make_counter()	# counter1和counter2是独立的闭包, 互不影响

print(counter1())  # 输出 1
print(counter1())  # 输出 2
print(counter2())  # 输出 1
```

### **应用场景**

1. 数据封装: 使用闭包可以创建私有变量, 只能通过闭包内的函数访问

    ```python
    def make_account(balance):
        def withdraw(amount):
            nonlocal balance
            if balance >= amount:
                balance -= amount
                return balance
            else:
                return "余额不足"
        return withdraw
    
    my_account = make_account(100)
    print(my_account(50))  # 输出 50
    print(my_account(60))  # 输出 "余额不足"
    ```
2. 回调函数: 闭包可以用于传递回调函数, 记住上下文环境
3. 装饰器: 闭包是实现装饰器的基础, 用来为函数动态添加功能

### **闭包和普通函数的区别**

|**普通函数**|**闭包**|
| ------------------------------| --------------------------------------------------|
|没有'记住'外部变量的能力|能够记住外部函数的变量, 即使外部函数已经执行完毕|
|作用域受限于当前的执行环境|闭包保存了自由变量的引用, 可以在不同作用域中使用|
|变量的生命周期受限于函数调用|变量的生命周期可以延长, 直到闭包不再被引用|

### **注意**

- `nonlocal`​关键字: 如果需要在闭包中修改外层函数的变量, 必须使用`nonlocal`​声明
- 可能导致内存泄漏: 如果闭包持有过多不必要的引用, 可能会导致内存无法释放
- 避免过度依赖闭包: 虽然闭包非常强大, 但在某些场景下可能会导致代码可读性下降

## 装饰器

Python中一种高级函数, 用于在不改变原函数代码的情况下, 动态地为函数或方法添加功能(可复用, 可多个修饰一个函数)

- 原则: 封闭开放, 定义好的功能不再修改, 增加额外的功能
- 本质: 是一个函数, 它接受另一个函数作为参数, 对其进行扩展或修改后返回一个新的函数
- 优点:

  - 代码复用: 通过装饰器, 可以为多个函数动态添加相同的功能
  - 分离逻辑: 把通用功能(如日志、权限验证)与业务逻辑分离
  - 简洁优雅: 避免重复代码, 提高代码可读性
- 若有多个装饰器, 从上到下执行, 原函数只调用一次
- 结构:

  ```python
  def decorator(func): 	# func即被装饰的函数
      def wrapper(*args, **kwargs):	# 包装函数, 在此添加额外功能
          # 在调用原函数之前可以执行一些操作
          do Something
          # 调用原函数
          result = func(*args, **kwargs)
          # 在调用原函数之后可以执行一些操作
          do Something
          return result
      return wrapper
  ```
- 使用: `@decorator_name`​置于函数定义上一行
- 带参数的装饰器(装饰器本身有参数):

  ```python
  # 接受一个times参数, 返回一个装饰器
  def repeat(times):		# 装饰器工厂, 用来生成装饰器
  	# 接受一个func参数, 返回一个包装函数
      def decorator(func):
          def wrapper(*args, **kwargs):
              for _ in range(times):
                  func(*args, **kwargs)
          return wrapper
      return decorator
  
  @repeat(times=3)
  def greet():		# 实际上被替换为wrapper函数
      print("Hello!")
  
  greet()
  ```
- 应用场景:

  - 日志记录:

    ```python
    def log(func):
        def wrapper(*args, **kwargs):
            print(f"Function {func.__name__} is called with {args}, {kwargs}")
            return func(*args, **kwargs)
        return wrapper
    
    @log
    def multiply(a, b):
        return a * b
    
    print(multiply(2, 5))
    ```
  - 权限验证:

    ```python
    def require_auth(func):
        def wrapper(user, *args, **kwargs):
            if not user.get("is_admin"):
                print("Access denied.")
                return None
            return func(user, *args, **kwargs)
        return wrapper
    
    @require_auth
    def delete_user(user, user_id):
        print(f"User {user_id} has been deleted.")
    
    # 示例
    admin = {"username": "admin", "is_admin": True}
    guest = {"username": "guest", "is_admin": False}
    
    delete_user(admin, 123)  # 有权限
    delete_user(guest, 456)  # 无权限
    
    # ------------------------------ # 
    # 输出
    User 123 has been deleted.
    Access denied.
    ```
  - 缓存结果: 将最近调用的输入参数及结果缓存, 极大的提高效率, 若有新的调用, 先检查缓存, 维护一个固定大小的有序字典, 存储参数和对应的返回值

    ```python
    def cache(func):
        cached_results = {}
    
        def wrapper(*args):
            if args in cached_results:
                print("Fetching from cache...")
                return cached_results[args]
            result = func(*args)
            cached_results[args] = result
            return result
        return wrapper
    
    @cache
    def fibonacci(n):
        if n <= 1:
            return n
        return fibonacci(n - 1) + fibonacci(n - 2)
    
    print(fibonacci(10))  # 计算并缓存结果
    print(fibonacci(10))  # 从缓存中获取
    ```

    - `@lru_cache`: Least Recently Used cache缓存装饰器, maxsize指定缓存的最大条目数

      ```python
      from functiontools import lru_cache
      
      @lru_cache(maxsize=256)  
      def fib(n): 
      	if n in (1, 2): 
      		return 1 
      	return fib(n - 1) + fib(n - 2)
      ```
  - 确保枚举唯一: `@unique`​, 修饰枚举类, 若枚举类中有重复值, 会抛出ValueError异常, 枚举成员之间只能用is比较, 不能用==

    ```python
    from enum import Enum, unique
    
    @unique
    class Color(Enum):	# 继承Enum表示定义的是一个枚举类, 包含一组有意义的常量集合, 但不保证唯一性
    	RED = 1			# Enum允许多个枚举成员有相同值
    	GREEN = 2
    	BLUE = 3
    ```
  - 加速计算：`@jit`​，即时编译，将数值函数编译成接近机器语言的高效代码

    函数必须是数值密集型、循环密集型才有效果，不支持所有Python特性（复杂类，字符串操作等），第一次运行慢（编译），推荐`nopython=True`​，否则会自动fallback到Python模式，提升不大

    ```python
    @jit(nopython=True)
    def foo(x):
    	pass
    ```
  - 实现单例模式:

    ```python
    def singleton(cls):
        """装饰类的装饰器"""
        instances = {}
    
        @wraps(cls)
        def wrapper(*args, **kwargs):
            if cls not in instances:
                instances[cls] = cls(*args, **kwargs)
            return instances[cls]
    
        return wrapper


    @singleton
    class President:
        """总统(单例类)"""
        pass
    
    # 线程安全的单例装饰器
    from functools import wraps
    form threading import RLock
    def singleton(cls):
        """线程安全的单例装饰器"""
        instances = {}
        locker = RLock()
    
        @wraps(cls)
        def wrapper(*args, **kwargs):
            if cls not in instances: 	# 检查两次比先锁后查性能更好
                with locker:		# 锁对象本就是上下文管理器对象
                    if cls not in instances: 
                        instances[cls] = cls(*args, **kwargs)
            return instances[cls]
    
        return wrapper
    ```
- `@wraps(func)`: 装饰器会覆盖原函数的元信息(函数名`__name__`​、函数文档字符串`__doc__`​、函数注解`__annotations`​), `functools.wraps`​可将原函数的元信息保留在包装函数中

  ```python
  from functools import wraps
  
  def decorator(func):
      @wraps(func)
      def wrapper(*args, **kwargs):
          return func(*args, **kwargs)
      return wrapper
  
  @decorator
  def my_function():
      """This is my_function."""
      pass
  
  print(my_function.__name__)  # 输出: my_function
  print(my_function.__doc__)   # 输出: This is my_function.
  ```

  - `@wraps`​工作原理: 将func的元信息复制到包装函数, 并设一个属性`__wrapped__`​指向原函数`func`​

    ```python
    def wraps(func):
        def decorator(wrapper):
            wrapper.__name__ = func.__name__
            wrapper.__doc__ = func.__doc__
            wrapper.__annotations__ = func.__annotations__
            wrapper.__wrapped__ = func
            return wrapper
        return decorator
    ```
  - 使用装饰器后, 原函数文档可通过help函数查看
  - 可通过`func.__wrapped__`​方式获得被装饰之前的函数/类

## 递归函数

函数调用自身

实现递归的思路: 

- 找到临界值(临界条件), 如0, 1
- 找到两个循环间的关系
- 总结规律

注意: 

- 一定要有基例(出口, 已知项)
- 嵌套的层次不可太深(占用内存)

‍

---

‍

# 五、模块管理

## 程序编译

- 代码开始执行后, 启动Python解释器, 将.py文件编译为**字节码对象PyCodeObject**(包含源代码中的字符串、常量值、字节码指令及字节码指令和源代码行号的对应关系)

  - `dis.dis.()`: 接收一个code对象, 输出code对象中字节码指令信息

    ```python
    import dis
    
    s = open('demo.py').read()
    co = compile(s, 'demo.py', 'exec')
    dis.dis(co)
    ```
- Python虚拟机从PyCodeObject对象中依次读入每一条字节码指令, 并在当前的`上下文环境`​中执行这条字节码指令
- 执行完后, 编译结果保存为.pyc文件, 下次不用再编译, 直接加载到内存

  - .pyc文件: 字节码对象在硬盘上的表现形式, 包含三部分信息:

    - Python的**magic number**: Python定义的一个用来保证Python兼容性的整数值, 不同版本的Python实现都会定义不同的magic number, 检查magic number即可限制低版本编译的pyc文件不能让高版本的Python程序来执行
    - .pyc文件创建的时间信息
    - PyCodeObject对象
  - 创建.pyc文件: `python generate_pyc.py module_name`​
  - Python只会对那些以后可能继续被使用和载入的模块才会生成pyc文件, 即被import的模块有pyc文件, 而**<u>主模块不生成pyc文件</u>**

## 引入import

将模块、函数、类或变量加载到当前的命名空间

### 过程

- 确保最新: import时检查.pyc文件中的创建时间信息

  - 若存在pyc文件, Python检查创建时间是否晚于代码文件的修改时间, 否则直接载入而省去编译过程
  - 若不存在pyc文件, 先将.py文件编译生成.pyc文件, 即import指令执行后就生成pyc文件
- <u>**将module的代码执行一遍**</u>: 创建函数(否则无法调用), 并在该module的命名空间中, 创建模块内定义的函数和各种对象的符号名称(也就是变量名), 并将其绑定到对象上, 使其他module能通过变量名来引用这些对象
- 缓存: 缓存已被import的module到一个全局modules集合`sys.modules`​(dict类型), 再次被import时从缓存中返回

### 引入方式

- 动态引入: 程序运行时根据需要加载模块, 灵活, 节省初始加载时间, 通过importlib模块或`__import__`​实现

  ```python
  # importlib模块
  import importlib
  module_name = "math"
  math_module = importlib.import_module(module_name)
  print(math_module.sqrt(16))  # 输出 4.0
  
  # __import__方法
  math_module = __import__('math')
  print(math_module.sqrt(16))  # 输出: 4.0
  ```
- 静态引入: 最常见, 在代码编写阶段就明确加载某个模块, 可能加载不必要的部分, 影响效率

  - `import module`: 导入模块中所有的代码元素, 访问变量时需要在变量前加`模块名.`​, 加`.`​的方式只能用于全部导入
  - `from module import class, func`: 导入模块中的类、函数等, 多个中间用逗号隔开, 不用在变量前加`模块名.`​
  - `from module import *`: 只能导入模块中`__all__`​规定的对外公开的内容, 单个/多个导入则无此限制
  - 使用from导入相同功能名时, 调用的是最后定义或导入的功能
  - `import module as 别名`​, 解决重名或过长问题
  - 导入快捷键: 在写好的模块名上按command+Enter, 再按一次enter
- 绝对引入: 从项目的根目录开始定位模块路径, 推荐, 便于维护
- 相对引入: 基于当前模块的位置进行模块加载, 使用点号表示层级

  - 适用于模块间内部依赖
  - `.`​当前目录、`..`​上一级目录、`...`​上上级目录, 以此类推

  ```python
  project/
      ├── main.py
      ├── utils/
      │   ├── helper.py
      │   └── data.py
  
  # 当前在utils/helper.py中:
  # 从当前目录引入 data.py
  from . import data
  # 从上一级目录引入 main.py
  from .. import main
  ```

### 定位顺序

Python解释器对模块位置的搜索顺序:

1. 当前目录
2. 若无, 则搜索shell变量PYTHONPATH下的每个目录
3. 若无, 则查看默认路径, UNIX下, 默认路径一般为/usr/local/lib/python

## 程序结构

### 模块

Module, 一个.py文件, modules中有一个主module作为程序入口

`__name__`: 模块的内置属性, 定义当前类/模块/脚本的名称, Python程序启动后, 自动为每个模块设置`__name__`​, 一般用文件名, 主模块例外, 被设置为`'__main__'`​

建议每个.py文件都使用`if __name__ == '__main__':`​

- if之前的代码**无条件执行**, 无论是作为主模块还是被导入
- if之后的代码只有脚本作为主模块运行时执行

### 包

Package, 有联系的模块的集合, 与同路径下`__init__.py`​文件共同组成包

`__init__.py`: 包中的特殊文件, 导入包时解释器自动执行包中`__init__.py`​文件

作用:

- 初始化包: 导包自动执行, 需执行一些初始化操作
- 标识包: 告诉解释器此文件夹为包, 而不是普通文件夹
- 控制包的导入行为:

  - 在`__init__.py`​中定义`__all__`​变量, 控制包的公共接口, 指定以`from module import *`​方式被导入时被导入的模块或子包, 以`import xxx`​方式导入的不受`__all__`​变量影响, 

    `__all__`​是包含**字符串**的列表, 可为模块名、类名、函数名、变量名等, 无后缀, 若函数不在该变量中, 则不能导入, 若无`__all__`​变量, `from module import *`​会导入所有不以下划线开头的名称(类、函数等)
- 提供包级别的功能: 包被导入时, 导入者可直接访问该包`__init__.py`​中定义的函数和类等, 而无需再显式导入该包中具体模块
- 自动导入模块: 在`__init__.py`​中将包内部模块导入, 用户只需导入包即可

  ```python
  # my_package/__init__.py
  from .module1 import func_from_module1
  from .module2 import func_from_module2
  __all__ = ['func_from_module1', 'func_from_module2']
  ```

### 库

Python中没有library概念, 库即可以为一个模块, 也可以是一个包

### Wheel

Python的一种打包格式, 让库的分发和安装变得更快、更简单

Python中第三方库的分发和安装通常有两种格式:

1. Source Distribution (sdist): 含源代码包, 需要在用户机器上编译, 安装时可能需要依赖编译工具链, 如 `gcc`​、`make`​
2. Wheel (Binary Distribution): <u>二进制格式</u>, 预先编译好了, 不需要用户在本地编译, 文件扩展名是 `.whl`​, 安装更快, 因为跳过了编译步骤, 可以针对不同的平台和Python版本打包, 减少兼容性问题, 可自动解决依赖问题

`build wheel`: 把源代码打包成.whl文件的过程

## 第三方模块

- 安装: pip指令

  ```shell
  pip -V  	# 查看pip版本, 大写V
  pip list 	# 查看所有已安装扩展
  python.exe -m pip install -upgrade pip: 更新pip版本
  pip install 模块名
  
  # 若未指定版本, 默认安装最新版
  pip uninstall 模块名
  
  # 卸载指定模块
  pip install 模块名 -i https://pypi.douban.com/simple/
  
  # 设置临时国内镜像
  pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
  ```
- pycharm安装: 文件 -> 设置 -> 项目 -> 解释器 : +安装扩展, -卸载扩展
- 数分三剑客: Numpy、Pandas、Matplotlib
- glob: 大批量文件处理

## 时间模块

### 两概念

1. 时间戳: 1970年1月1日0时0分0秒(UNIX纪元)至今的累积时间, 浮点数

2. 结构体时间: struct\_time/时间元组类型, 有9个元素, 被函数当做输入参数, 用于函数间传递时间值, 可与时间戳互相转换, 包括:

    - tm\_year: 年份, 四位数整数
    - tm\_mon: 月份, 1到12的整数
    - tm\_mday: 日期, 1到31的整数
    - tm\_hour: 小时, 0到23的整数
    - tm\_min: 分钟, 0到59的整数
    - tm\_sec: 秒数, 0到59的整数
    - tm\_wday: 星期几, 0到6的整数 0表示星期一, 1表示星期二
    - tm\_yday: 一年中的第几天, 1到366的整数
    - tm\_isdst: 是否为夏令时, -1、0或1, 默认为-1 -1表示夏令时信息不可用, 0表示不是夏令时, 1表示是夏令时

### time模块

获取展示时间信息的标准库, 三功能:

- 时间处理

  - `time.time()`: 获取当下时间戳, 默认为秒数
  - `time.gmtime()`: 返回格林威治时间戳对应的时间元组
  - `time.ctime()`: 返回系统当前时间戳对应的易读格式字符串时间(周几, 月份, 号数, 时分秒, 年)
  - `time.localtime()`:返回系统当前时间的时间戳对应本地时间的时间元组
- 时间格式化

  - `time.mktime(t)`: 将时间元组变量t转换成时间戳
  - `time.strftime(format, t)`: 根据format格式显示时间

    format: 由格式控制符组成的格式定义字符串

    t: 代表时间的时间元组变量

    ```python
    print(time.strftime("%Y-%m-%d %H:%M:%S"))	# 2023-04-29 17:27:51
    ```

  - `time.strptime(string, format)`: 根据format格式定义解析字符串string, 返回struct\_time类型时间变量, 要求解析格式和时间字符串一致

    string: 时间的字符串
  - `time.asctime([t])`: 接受时间元组并返回形式为`"Tue Dec 11 18:07:14 2008"`​的24个字符的字符串
- 计时

  - `time.sleep(secs)`: 将当前程序挂起secs秒, 即休眠, secs表示时间的数值, 整数或者浮点数
  - `time.perf_counter()`: 返回一个代表时间的精确的浮点数, 相比采用时间戳更为准确, 用于精确计时, 用两次或多次调用的差值来计时, 单次调用返回值没有意义, 系统提供的最精确的计时方法

    ```python
    t1 = time.perf_counter()
    time.sleep(5)
    t2 = time.perf_counter()
    print(t2 - t1) 			# 5.000081919965048
    ```

### timeit模块

用于统计小段代码执行时间

`timeit.timeit()`​, 默认执行一百万次

### datetime模块

基于time模块, 更强大, 用于显示和设置日期时间

- datetime类: date类和time类的综合使用

  - `datetime.datetime()`: 创建datetime对象, 第一个datetime为模块名, 第二个为类名

    ```python
    date1 = datetime.datetime(year=2024, month=5, day=12, hour=12, minute=34, second=23,  microsecond=12,  tzinfo=0)
    # 年月日不可省略, 其他都可省略, 默认值为0
    ```
  - `timestamp()`: 时间对象转时间戳
  - `datetime.datetime.now(tz=None)`: 获取指定时区的当前日期和时间, `tz\=None`​等同于today()

    可继续获取年、月、日等, 在后面继续`.year`​/`.month`​/`.weekday`​等

    可继续`.strftime()`​, 时间格式化
  - `datetime.today()`: 返回当前的本地日期和时间
  - `datetime.fromtimestamp(timestamp, tz=None)`: 返回与UNIX时间戳对应的本地日期和时间
  - `datetime.datetime.strptime(date_str, format)`: 时间反格式化
- date类: 主要用于处理年月日

  - `datetime.date(year, month, day)`: 创建date对象, 若指定的day参数超出范围会报错
  - `date.today()`: 返回当前的本地日期
  - `date.fromtimestamp(timestamp)`: 返回与UNIX时间戳对应的本地日期
- time类: 主要用于处理时分秒

  `datetime.time(hour=0, minute=0, second=0, microsecond=0, tzinfo=None)`: 创建time对象
- timedelta类: 主要用于计算时间跨度

  - `datetime.timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours=0, weeks=0)`: 创建对象
  - microsecond: 微秒, milliseconds: 毫秒
  - 所有参数可为整形/浮点型/正数/负数
  - 时间 +/- `datetime.timedelta(days=)`​
- tzinfo类: 时区类
- 与字符串类型互换

  - 日期时间对象转字符串/日期时间格式化: `对象名.strftime(format)`​
  - 字符串转日期时间对象/日期时间解析: `datetime.strptime(date_string, format)`​
  - 格式控制符

    ![截屏2024-12-23 19.02.38](../assets/截屏2024-12-23%2019.02.38-20241223190242-ju94oif.png)![截屏2024-12-23 19.28.26](../assets/截屏2024-12-23%2019.28.26-20241223192829-xwd6p03.png)​

    %j: 一年中的第几天

### calendar模块

- `.calendar(year)`: 获取某年的日历
- `.month(year, month)`: 获取某年某月的日历
- `.isleap()`: 判断是否是闰年, 闰年True
- `.leapdays(year1, year2)`: 返回两指定年份之间闰年的个数, 不包含结束年
- `.weekday(year, month, day)`: 返回指定天的星期, 星期一: 0  星期二: 1 …… 星期日: 6
- `.monthrange(year, month)`: 返回二元元组, 第一个数为该月第一天的星期, 第二个数为该月总天数

  只拿天数`calendar.menthrange( )[-1]`​

## re模块

用于检索、替换符合指定格式的文本, 独立语法, 独立处理引擎, 适用于所有编程语言

1. 构建正则对象: `对象 = re.compile(正则表达式, flag)`​或`对象.方法(字符串)`​, flag为模式修正符, 可选参数
2. `re.方法(正则表达式, 字符串, flag)`​, 字符串一般配合r符不需写转义符
3. 方法:

    - `.match(pattern, string, flags=0)`: 从第一个字符匹配, 用于检查字符串是否以指定字符开头, 返回Match对象
    - `.fullmatch()`: 从头到尾完全匹配, 等价于`match('^ $')`​
    - `.search(pattern, string, flags=0, pos)`: 整个字符串中匹配, 返回Match对象, 只返回第一个满足条件的, 无则返None, pos控制从字符串的哪个索引开始查找

      - `re.Match类`: 包含匹配的详细信息, 例如匹配的位置、内容等

        - `Match.group()`: 返回匹配的具体内容
        - `Match.start()`: 返回匹配内容的开始索引
        - `Match.end([group])`: 返回指定组匹配的结束位置的**索引**(以字符串开头为基准, <u>右边界的索引+1</u>, group可省, 默认为整个匹配, 即组号0
        - `Match.span()`: 返回`(start, end)`​元组

        ```python
        # 通过search函数指定搜索位置找出所有匹配
        m = pattern.search(sentence)
        while m:
            print(m.group())
            m = pattern.search(sentence, m.end())
        ```
    - `.findall()`: 查找所有满足条件的内容, 返回匹配内容**列表**, 无则返回空列表

      - 带捕获组: 使用`()`​可返回`()`​中的内容, 列表形式, 若有n个(), 列表元素为n元元组

        ```python
        text = "Name: Alice, Age: 25; Name: Bob, Age: 30"
        pattern = r"Name: (\w+), Age: (\d+)"  # 捕获名字和年龄
        result = re.findall(pattern, text)
        print(result)  # 输出: [('Alice', '25'), ('Bob', '30')]
        
        text = "2023-01-01, 2024-12-31"
        pattern = r"(\d{4})-(\d{2})-(\d{2})"  # 捕获年、月、日
        result = re.findall(pattern, text)
        print(result)  # 输出: [('2023', '01', '01'), ('2024', '12', '31')]
        ```
    - `.finditer()`: 同findall, 返回迭代器
    - `.split(pattern, str, maxsplit=0)`: 用指定正则内容分割字符串, 返回**列表**, maxsplit为最大分割次数, 为0表示不限次数, 常搭配+使用
    - `.sub(正则, 新内容, 字符串, count=0)`: 用新内容替换字符串中指定正则内容, 不显示替换次数, count为指定替换次数, 为0表示不限次数
    - `.subn(正则, 新内容, 字符串, count=0)`: 显示替换次数
    - `.compile(pattern, flags=0)`: 编译正则表达式返回正则表达式对象, 可直接调用正则对象的方法, 而不是re的方法传参

      - 若某正则表达式需重复使用, 最好先compile减少解析和编译的次数
    - `.purge()`: 用于清除隐式编译的正则表达式的缓存, 在内存受限条件下使用, 极少使用(`re`​模块会缓存最近使用的正则表达式, 最多缓存512个)

      - 调用re函数时, 会发生以下隐式流程:

        - 正则解析: 将正则表达式字符串转化为内部的正则表达式对象, 即re.Pattern类的一个实例
        - 正则编译: 正则引擎会将这个解析后的正则表达式转换为字节码, 用于高效匹配
        - 匹配: 正则引擎会用生成的字节码去匹配目标字符串
        - 返回结果

## string模块

- `whitespace = ' \t\n\r\v\f'`: 空白符有个空格, 易忘
- `ascii_lowercase = 'abcdefghijklmnopqrstuvwxyz'`​
- `ascii_uppercase = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'`​
- `ascii_letters = ascii_lowercase + ascii_uppercase`​
- `digits = '0123456789'`​
- `hedigits = digits + 'abcdef' + 'ABCDEF'`​
- `octdigits = '01234567'`​
- `punctuation = f""!"#$%&'()*+,--/:;<=>?@[\]^_{|}`​
- `printable = digits + ascii_letters + punctuation + whitespace`​

## copy模块

- `.copy()`: 浅拷贝/浅复制/影子克隆, 创建新对象, 并复制原对象**属性**中**基本数据类型的值**和**引用类型的引用**

  - 不看其本身是基本数据类型/引用类型, 只看元素类型
  - 与简单赋值区别: =赋值未创建新对象, 浅拷贝创建对象
- `.deepcopy()`: 深拷贝/深复制/深度克隆, 开辟新栈, 新旧无关, 复制值而不是引用

> 浅拷贝相较简单赋值更进一步, 创建了新对象并复制了基本类型元素的值
>
> 深拷贝相较浅拷贝更进一步, 复制了引用类型元素的值

## warnings模块

```python
warnings.filterwarnings('ignore') 	# 警告过滤器
```

## random模块

- `.random()`: [0, 1)区间随机小数
- `.uniform(a, b)`: 指定闭区间随机**实数**
- `.randint(start, end)`: 指定范围一个随机整数, 包含结束值
- `.choice()`: 指定范围随机选择一个
- `.choices(序列, k)`: 指定范围随机选择多个, 有放回式抽样, k为样本数
- `.sample(序列, k)`: 指定范围随机选择多个, 无放回, 要求序列本身不重复, 样本数k大于序列长度会报错
- `randrange(start, end, step)`: 指定递增数列中随机选择一个数, 不包含结束值, 可省略step
- `shuffle()`: 打乱列表, 修改原列表

## math模块

提供复杂浮点数学运算的函数, 如幂指对三角、双曲线等

- `.fabs()`: 绝对值, 返回浮点型
- `.fsum()`: 无精度损失求和, 返回浮点型
- `.pow(x, y)`: 返回x的y次幂的值, 返回浮点型
- `.factorial()`: 阶乘
- `math.ceil()`: 向上取整, 注意负数的问题
- `math.floor()`: 向下取整
- `math.sqrt()`: 开平方
- `math.modf()`: 浮点数分割, 结果为元组
- `math.log(x, base)`: 返回以base为底的x的对数, 若省略base, 则计算x自然对数
- `sin()`: 返回弧度x的三角正弦
- `degrees(x)`: 将弧度x转换为角度
- `radians(x)`: 将角度x转换为弧度
- `math.e`​、`math.pi`​、`math.nan`​、`math.inf`​等常量

## os模块

### 文件(夹)操作

- 创

  - `os.mkdir()`: 创建文件夹 存在则报错

    ```python
    if not os.path.exists('名'):  
    	os.mkdir('名')
    ```
  - `os.makedirs()`: 递归式创建文件夹, `/`​隔开各层, 参数exist_ok=True: 文件不存在时则创建
- 查

  - `os.listdir()`: 查看指定目录下所有文件和文件夹, 只能看一层
  - `os.curdir()`: 获取当前目录
  - `os.getcwd()`: 获取当前绝对路径
  - `os.walk()`: 遍历源文件夹下所有子目录和文件

    ```python
    for root, dirs, files in os.walk(source_dir):
    	pass
    ```
  - `os.path.abspath()`: 获取相对路径对应的绝对路径
  - `os.path.basename()`: 获取路径的最后一级路径名
  - `os.path.dirname()`: 获取包含最后一级路径的内容 即除最后一级之外的所有
  - `os.path.splitext()`: 返回二元元组, 文件则返回(文件名, 后缀), 文件夹则返回(目录, '空串')
  - `os.path.getsize()`: 获取文件大小
  - `os.path.exists()`: 判断文件是否存在, 返回布尔值
  - `os.path.isfile()`: 判断是否是文件, 返回布尔值, 文件夹为否
  - `os.path.isdir()`: 判断是否是文件夹, 返回布尔值, 文件为否
  - `os.path.isabs()`: 判断是否是绝对路径
- 改

  - `os.rename('原名', '新名'或'新路径'`: 路径不变名字变: 重命名, 名字不变路径变: 实现剪切
  - `os.path.join()`: 路径拼接, 拼成一个完整的路径
  - `os.path.split()`: 路径拆分
- 删

  - `os.rmdir()`: 删除文件夹, 不存在则报错

    ```python
    if os.path.exists('demo'): os.rmdir('demo')
    ```
  - `os.removedirs()`: 删除多级文件夹, 需为空文件夹, 不能有文件
  - `os.remove()`: 删除文件, 需为当前文件所在目录下的

### system操作

- `os.system('clear')`: 清屏

## shutil模块

标准库模块, 提供高级的文件操作和文件系统操作功能, 简化文件系统任务

- 复制:

  - `shutil.copy(src, dst)`: 复制文件(包括文件内容和权限)
  - `shutil.copy2(src, dst)`: 复制文件, 除了文件内容和权限外, 还会复制文件的元数据(例如创建时间和修改时间)
  - `shutil.copytree(src, dst)`: 递归地复制整个目录树

- 移动: `shutil.move(src, dst)`: 移动文件或目录到目标路径, 若目标路径已经存在, 该路径中的内容会被覆盖
- 删除:

  - `shutil.rmtree(path)`: 递归删除整个目录树及其内容
  - `shutil.remove(filename)`: 删除指定的文件
- 压缩和解压:

  - `shutil.make_archive(base_name, format, root_dir=None, base_dir=None)`: 创建一个压缩文件,

    - `base_name`​ 是压缩文件的路径
    - `format`​ 压缩格式, `'zip'`​/`'tar'`​
  - `shutil.unpack_archive(filename, extract_dir=None, format=None)`: 解压缩压缩文件, 可以根据文件扩展名自动检测解压缩格式, 也可以手动指定
- 磁盘使用情况: `shutil.disk_usage(path)`​, 获取指定路径的磁盘使用情况, 返回一个包含总空间、已用空间和剩余空间的命名元组
- 修改文件权限: `shutil.chown(path, user=None, group=None)`​, 更改文件的拥有者和/或所属组, 可以分别指定用户和组(或者只修改其中一个)
- 文件描述符操作: `shutil.get_terminal_size()`​, 获取终端的大小(宽度和高度)

## heapq模块

堆排序

```python
"""
从列表中找出最大的或最小的N个元素
堆结构(大根堆/小根堆)
"""
import heapq

list1 = [34, 25, 12, 99, 87, 63, 58, 78, 88, 92]
list2 = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
print(heapq.nlargest(3, list1))
print(heapq.nsmallest(3, list1))
print(heapq.nlargest(2, list2, key=lambda x: x['price']))
print(heapq.nlargest(2, list2, key=lambda x: x['shares']))
```

## itertools模块

迭代工具模块

- `islice()`: 窗口迭代器, 返回一个运行在序列的子分组之上的迭代器
- `tee()`: 往返式迭代器
- `groupby()`: uniq迭代器, 只能分组相邻的元素, 使用前需要排序
- `chain(*iterables)`: 创建在第一个可迭代对象上迭代的迭代器, 然后继续下一个
- `count([n])`: 返回一个给出连续整数的迭代器
- `cycle(iterable)`: 在可迭代对象的每个元素上迭代, 然后重新开始, 无限重复
- `dropwhile(predicate, iterable)`: predicate为真则从可迭代对象中删除元素
- `ifilter(predicate, iterable)`: 类似filter()
- `ifilterfalse(predicate, iterable)`: 类似ifilter, 但在predicate为假时迭代
- `imap(function, *iterables)`: 类似map(), 在多个可迭代对象上工作, 在最短的可迭代对象耗尽时停止
- `izip(*iterables)`: 类似zip(), 返回一个迭代器
- `repeat(object[, times])`: 返回一个迭代器, 每次调用迭代器时返回object, 运行times次, 默认无限
- `starmap(function, iterable)`: 类似imap, 将可迭代元素作为星号参数传递给function
- `takewhile(predicate, iterable)`: 从可迭代对象返回元素, 当predicate为假时停止

```python
import itertools

# 产生ABCD的全排列
itertools.permutations('ABCD')

# 产生ABCDE的五选三组合
itertools.combinations('ABCDE', 3)

# 产生ABCD和123的笛卡尔积
itertools.product('ABCD', '123')

# 产生ABC的无限循环序列
itertools.cycle(('A', 'B', 'C'))
```

## collections模块

常用的工具类:

- `namedtuple`: 命令元组, 是一个类工厂, 接受类型的名称和属性列表来创建一个类
- `deque`: 双端队列, 列表的替代实现, Python中的列表底层是基于数组来实现的, 而deque底层是双向链表, 因此当你需要在头尾添加和删除元素时, deque会表现出更好的性能
- `Counter`: `dict`​的子类, 键是元素, 值是元素的计数, `most_common()`​获取出现频率最高的元素
- `OrderedDict`: `dict`​的子类, 记录键值对插入的顺序, 看起来既有字典的行为, 也有链表的行为
- `defaultdict`: 类似于字典类型, 但是可以通过默认的工厂函数来获得键对应的默认值, 相比字典中的`setdefault()`​方法, 这种做法更加高效

```python
"""
找出序列中出现次数最多的元素
"""
from collections import Counter

words = [
    'look', 'into', 'my', 'eyes', 'look', 'into', 'my', 'eyes',
    'the', 'eyes', 'the', 'eyes', 'the', 'eyes', 'not', 'around',
    'the', 'eyes', "don't", 'look', 'around', 'the', 'eyes',
    'look', 'into', 'my', 'eyes', "you're", 'under'
]
counter = Counter(words)
print(counter.most_common(3))
```

## pylint/flake8

提升代码质量

- pylint: 可集成到pycharm中, 插件或外部工具, shell命令: `pylint module.py`​, 会生成分数和评价
- flake8: 效果同pylint

# 六、文件操作

- 文件类型

  - 文本文件: 以字符形式存储数据, 字符有编码
  - 二进制文件: 以字节形式存储数据, 无编码, 如exe, jpg, png, word, excel, ppt等
- 读写文本文件

  - `read(size=-1)`: 从文件中读取字符串, size限制字符数, -1表示无限制
  - `readline(size=-1)`: 在读取到换行符或文件尾时返回单行字符串, 若已到文件尾, 则返回空字符串
  - `readlines()`: 读取文件数据到一个字符串列表中, 每一行都是列表的一个元素
  - `write(s)`: 将字符串s写入文件中, 并返回写入的字符数
  - `writelines(lines)`: 向文件中写入字符串列表, 不加行分隔符, 故通常为每一行末尾提供行分隔符
  - `flush()`: 刷新写缓冲区, 在文件没有关闭的情况下将数据写入文件中
- 读写二进制文件

  - 单位为字节, 无编码问题
  - `read(size=-1)`: 从文件中读取字节, size限制字节数, -1表示无限制
  - `readline(size=-1)`: 从文件中读取并返回一行
  - `readlines()`: 读取文件数据到一个字节列表中, 每一行数据都是列表的一个元素
  - `write(b)`: 写入b字节, 并返回写入的字节数
  - `writelines(lines)`: 向文件中写入字节列表, 不加行分隔符, 故通常为每一行末尾提供行分隔符
  - `flush()`: 刷新写缓冲区, 在文件没有关闭的情况下将数据写入文件中
- 文件路径

  - 绝对路径: 从盘开始到具体文件结束
  - 相对路径

    - ./: 为正在编辑的文件所在的当前文件夹
    - ../: 为正在编辑的文件所在文件夹的上级文件夹
  - |路径表达式|含义|
    | ------------| ----------------------------------------|
    |​`.`​|文件所在的当前文件夹|
    |​`..`​|上一级文件夹|
    |​`/`​|从根节点选取(Mac为正斜`/`​, win为反斜`\`​)|

- `open()`: 打开文件, `open(file, mode='r', encoding=None, errors=None)`​

  - `file`​参数, 表示要打开的文件, 可以是字符串/整数类型

    - 字符串: 表示文件名, 可以是相对/绝对路径

    - 整数: 表示一个已经打开的文件
  - `mode`​参数, 用于设置文件打开模式, 字符串类型

    ![截屏2025-01-16 11.16.58](../assets/截屏2025-01-16%2011.16.58-20250116111701-ez6euo0.png)

    - `r`: 读取, 默认
    - `rb`: 以只读模式打开二进制文件
    - `t`: 以文本文件模式打开文件, 默认
    - `b`: 以二进制文件模式打开文件
    - `w`: 以只写模式打开文件, 不能读内容

      - 若文件不存在, 则创建文件, 仅限于最后一级不存在

      - 若文件存在, 则**覆盖**文件内容
    - `a`: 以追加模式打开文件, 不能读内容

      - 若文件不存在, 则创建文件, 仅限于最后一级不存在
      - 若文件存在, 则在文件末尾追加
    - `x`: 以独占创建模式打开文件

      - 若文件不存在, 则创建并以写入模式打开
      - 若文件存在, 则引发FileExistsError异常
    - `+`: 以更新(读写)模式打开文件, 可读可写, 必须与r/w/a组合使用才能设置文件为读写模式

    ![截屏2024-12-23 23.51.32](../assets/截屏2024-12-23%2023.51.32-20241223235135-na04eg3.png)​
  - `encoding`​参数: 用来指定打开文件时的编码, 默认None, 用操作系统默认的编码, 主要用于打开文本文件
  - `errors`​参数: 指定在发生错误时如何处理, 推荐取值为'ignore', 表示忽略该错误, 程序继续执行

- `close()`: 关闭文件

  - 推荐close放在finally代码块中, 保证对文件的操作无论正常结束与否都能关闭文件

    ```python
    finally:
    	if f is not None:
    		f.close()
    		print('关闭文件成功')
    ```
  - `with as`​上下文操作语句, 代码块结束之后自动释放资源、关闭文件

    ```python
    f_name = 'test.txt'
    with open(f_name) as f:
    	content = f.read()
    	print(content)
    ```

‍

---

‍

# 七、异常处理

- 尽可能用内置的异常处理语句替换try语句, 如with语句、getattr()方法(其底层逻辑已处理了异常)
- Traceback信息

  - 异常堆栈信息, 描述程序运行的过程及引发异常的信息
  - 异常发生文件、异常发生行数、异常发生表达式、异常信息描述
- 分类

  - Exception、Error、Warning 统称异常

    - `Exception`: 所有错误的父类
    - `ZeroDivisionError`: 任何数都不能除以0
    - `TypeError`​、`NameError`​、`KeyError`​、`IndexError`​、`IdentationError`​……
- 起别名: `except 异常 as e`: e为异常对象, 一个变量
- BaseException: Exception的父类, 还包含SystemExit、KeyboardInterruptd、GeneratorExit, 属于高级异常, 平时不应使用, 应该交给解释器处理
- 捕获异常

  - try-except语句: 可嵌套

    ```python
    try: 
    	可能引发异常的语句 		# 若try有异常, 执行except后面代码
    except [异常类型]:  			# 按顺序一次匹配 
    	代码
    except [异常类型]: 
    	代码
    except:  	# 异常类型若省略, 必须放在最后, 捕获上面未匹配的所有异常类, 相当于except Exception
    	代码
    ```

    - 多重异常捕获: `except (ZeroDivisionError, ValueError) as e:`​
  - else分支: 若try无异常, 则执行else, 可省略else, 若有else则必有except

    ```python
    try:
    	do something
    except:
    	code
    else:
    	code
    ```
  - finally分支: 无论try代码块是否正常结束, 都会执行finally代码块, 有finally的时候可省略except

    ```python
    try:
    	code
    [except:
    	code]
    finally:
    	code
    ```

    - 简化: `with as`​

      ```python
      with expression [as variable]:
      	with_block
      ```

      先执行`__enter__()`​函数(返回值赋给as后的variable, 若无as variable, 会忽略返回值)

      再执行with-block语句

      无论成败, 执行\_\_exit\_\_函数
  - 自定义异常类

    - class 自定义异常名(Exception): def \_ \_init\_ \_(self, message): super( ).\_ \_init\_ \_(message)
    - 需要继承Exception类或其子类
- 自定义异常类: 需继承Exception或其子类

  ```python
  class 自定义异常名(Exception):
  	def __init__(self, message):
  		super().__init__(message)
  ```
- raise语句: 主动抛出异常

  - 若捕获异常后想重新抛出, 用不带参数的raise即可(一行只有raise一个单词)

  ```python
  raise **Exception(message)
  ```
- assert语句: Python中一个调试工具, 用于开发阶段进行参数检查、错误检查和验证假设, 生产环境中`assert`​语句通常会被禁用(不应被用于实际的错误处理)

  - Python优化模式: 使用`-O`​参数运行Python, 忽略所有`assert`​语句

  ```python
  # 测试condition是否为真, False则抛AssertionError异常, True则无事发生
  # 可选择性地提供错误消息Error message
  assert condition [, "Error message"]
  
  assert x == 10, "x should be 10, but it is {}".format(x)
  assert age >= 18, "Age must be at least 18"
  ```
- Python原则之一: EAFP优于LBYL

  EAFP - Easier to Ask Forgiveness than Permission.  
  LBYL - Look Before You Leap.

  宁可用异常处理, 不做繁杂的条件控制

  ```python
  # 好的代码: 
  d={'x': '5'}
  try:
  	value = int(d['x'])
  	print(value)
  except (KeyError, TypeError, ValueError):
  	value = None
  ```

# 八、魔术方法

又称dunder方法, 是对象行为的一种**约定(非强制)** , 允许对象表现地像内置类型, 且可自定义行为, 而不需要继承特定类或接口

本质是一种行为规范, 通过约定, 确保对象在特定操作下表现出一致的行为

## 上下文管理协议

允许开发者自定义资源管理行为, 可自动化资源管理及异常处理

上下文管理器: 一个实现了上下文管理协议的对象, 告诉python在进入with时做什么, 退出时做什么, 本质是**资源管理器**

核心方法: `__enter__()`​和`__exit__()`​, 配合with语句

- `__enter__()`: 进入with语句时调用, 进行上下文管理器的初始化工作

  - 通常是资源的获取, 如打开文件、连接数据库、分配内存等,
  - 返回值: 可返回任何对象, 通常是资源本身(如文件句柄), 也可返回任何对象供as语句使用
- `__exit__()`: 退出with语句时调用, **无论代码块正常结束还是异常退出**

  - 通常用于清理, 如释放资源、关闭文件、断开数据路连接等
  - 返回值: 返回True时, 异常被抑制不会传播, 返回False或None则异常被重新抛出
  - 参数: ​

    - `exc_type`: 异常类型, 如果没有异常则为 `None`​​
    - `exc_value`: 异常实例, 如果没有异常则为 `None`​​
    - `traceback`: 异常的回溯对象, 如果没有异常则为 `None`​
- `with……as`: 避免手动管理, 文件在操作完成后自动关闭

  ```python
  with open('file.txt', 'w') as f: 	# open()返回一个文件对象, 即一个上下文管理器
  	f.write('Hello World!')			# as将返回的对象赋值给f
  ```

## **可迭代协议**

使对象可在for循环中被迭代

- `__iter__()`: 返回一个迭代器对象

## 迭代器协议

一种设计模式, 允许对象提供一种**统一**的方式来遍历其元素, 定义了如何创建和使用迭代器, 在不暴露底层数据结构的情况下, 依次访问集合中的元素

核心方法: 

- `__iter__()`: 返回一个迭代器对象, 该对象具有`__next__()`​方法
- `__next__()`: 逐个返回元素, 并记录当前位置, 若无后续元素, 抛StopIteration异常

> 可把`__iter__()`​协议理解为生成`__next__()`​协议的一个桥梁, next才是前移取数的完成者

为了统一接口, 迭代器本身也需`__iter__()`​协议, 返回自身, 即return self

iter()函数本质是对一个对象调用__iter__()方法

```python
print(iter(iterator) == iterator) 		# true
```

## 序列协议

使对象可像列表、元组、字符串等序列类型一样, 支持下标索引和 `len()`​ 函数

- `__getitem__(index)`: 返回给定索引的元素
- `__len__()`: 返回序列的长度

## **映射协议**

使得对象能够表现得像字典一样

- `__getitem__(key)`: 返回给定键的值
- `__setitem__(key, value)`: 设置给定键的值
- `__delitem__(key)`: 删除给定键
- `__contains__(key)`: 检查给定键是否存在于映射中

## **可调用对象协议**

使对象能够表现得像函数一样被调用

- `__call__(self, *args, **kwargs)`: 允许对象像函数一样被调用

  ```python
  class MyCallable:
      def __call__(self, x, y):
          return x + y
  
  c = MyCallable()
  print(c(3, 4))  # 输出 7
  ```

## **比较协议**

使对象可在比较操作中使用, 如`==`​、`!=`​、`<`​、`>`​ 等

- `__eq__(self, other)`: 判断 `self`​ 是否等于 `other`​
- `__ne__(self, other)`: 判断 `self`​ 是否不等于 `other`​
- `__lt__(self, other)`: 判断 `self`​ 是否小于 `other`​
- `__le__(self, other)`: 判断 `self`​ 是否小于或等于 `other`​
- `__gt__(self, other)`: 判断 `self`​ 是否大于 `other`​
- `__ge__(self, other)`: 判断 `self`​ 是否大于或等于 `other`​

## **容器协议**

通常用于实现`in`​操作

- `__contains__(self, item)`: 检查容器是否包含某个元素

## **自省协议**

使对象可控制它们如何转换为字符串, 常用于打印和调试

- `__str__()`: 返回对象的字符串表示
- `__repr__()`: 返回对象的官方字符串表示

## 描述符协议

描述符: 本质为实现了描述符协议的类, 可控制其他对象属性的访问

- 两种类型: 

  1. 数据描述符: 通常用来完全控制属性的访问, 同时实现了 `__get__`​ 和 `__set__`​ 方法
  2. 非数据描述符: 通常用来提供方法或计算的值, 不直接存储数据, 仅实现`__get__`​ 方法
- 优先级:

  - 数据描述符优先于实例属性
  - 非数据描述符的优先级低于实例属性
- 与`property`​关系: property只是描述符的简单封装, 适用于轻量级场景, 描述符用于复杂、可复用行为
- 描述符协议: 

  - `__get__(self, instance, owner)`: 定义属性被获取时的行为

    - self: 描述符对象本身
    - instance: 访问属性的实例对象
    - owner/klass: 实例所属的类

    ```python
    class Descriptor:
        def __get__(self, instance, klass):
            print(f"Accessing via {klass.__name__}.{instance.__class__.__name__}")
            return "Descriptor value"

    class MyClass:
        attr = Descriptor()

    obj = MyClass()
    print(obj.attr)  # 输出: Accessing via MyClass.MyClass
    ```

  - `__set__(self, instance, value)`: 定义属性被设置时的行为
  - `__delete__(self, instance)`: 定义属性被删除时的行为

‍

---

‍

# 九、算法设计

## 排序算法

- 冒泡排序: 

  ```python
  # 固定比较方法, 简单, 修改原数据
  def bubble_sort(arr):
  	n = len(arr)
  	for i in range(n):
  		for j in range(n-i-1):
  			if arr[j] > arr[j+1]:
  				arr[j], arr[j+1] = arr[j+1], arr[j]
  	return arr

  # 灵活, 自定义比较方法, 不改变原数据
  def bubble_sort(items, comp=lambda x, y: x > y):
      """冒泡排序"""
      items = items[:]
      for i in range(len(items) - 1):
          swapped = False
          for j in range(len(items) - 1 - i):
              if comp(items[j], items[j + 1]):
                  items[j], items[j + 1] = items[j + 1], items[j]
                  swapped = True
          if not swapped:
              break
      return items
  ```

- 搅拌排序: 冒泡排序升级版

  ```python
  def bubble_sort(items, comp=lambda x, y: x > y):
      """搅拌排序(冒泡排序升级版)"""
      items = items[:]
      for i in range(len(items) - 1):
          swapped = False
          for j in range(len(items) - 1 - i):
              if comp(items[j], items[j + 1]):
                  items[j], items[j + 1] = items[j + 1], items[j]
                  swapped = True
          if swapped:
              swapped = False
              for j in range(len(items) - 2 - i, i, -1):
                  if comp(items[j - 1], items[j]):
                      items[j], items[j - 1] = items[j - 1], items[j]
                      swapped = True
          if not swapped:
              break
      return items
  ```
- 选择排序: 

  ```python
  # 固定比较方法, 简单, 修改原数据
  def selection_sort(lst):
  	for i in range(len(lst) - 1):
  		min_index = i
  		for j in range(i + 1, len(lst)):
  			if lst[j] < lst[min_index]:
  				min_index = j
  		lst[i], lst[min_index] = lst[min_index], lst[i]
  	return lst

  # 灵活, 自定义比较方法, 不改变原数据
  def selection_sort(lst, comp=lambda x, y: x < y):
      """
      通用选择排序算法
    
      :param lst: 待排序列表
      :param comp: 比较函数, 默认为升序排序
      :return: 排序后的新列表
      """
      lst = lst[:]  # 复制列表, 避免修改原始数据
      for i in range(len(lst) - 1):
          min_index = i
          for j in range(i + 1, len(lst)):
              if comp(lst[j], lst[min_index]):
                  min_index = j
          lst[i], lst[min_index] = lst[min_index], lst[i]
      return lst

  ```

- 快速排序: 

  ```python
  def quick_sort(array, start, end):
  	if start >= end:
  		return
  	mid_data, left, right = array[start], start, end
  	while left < right:
  		while array[right] >= mid_data and left < right:
  			righy -= 1
  		array[left] = arrat[right]
  		while array[left] < mid_date and left < right:
  			left += 1
  		array[right] = mid_data
  	array[left] = mid_data
  	quick_sort(array, start, left-1)
  	quick_sort(array, left+1, end)
  
  # 灵活版本
  def quick_sort(items, comp=lambda x, y: x <= y):
      items = list(items)[:]
      _quick_sort(items, 0, len(items) - 1, comp)
      return items


  def _quick_sort(items, start, end, comp):
      if start < end:
          pos = _partition(items, start, end, comp)
          _quick_sort(items, start, pos - 1, comp)
          _quick_sort(items, pos + 1, end, comp)


  def _partition(items, start, end, comp):
      pivot = items[end]
      i = start - 1
      for j in range(start, end):
          if comp(items[j], pivot):
              i += 1
              items[i], items[j] = items[j], items[i]
      items[i + 1], items[end] = items[end], items[i + 1]
      return i + 1
  ```

- 归并排序: 

  ```python
  def merge(items1, items2, comp=lambda x, y: x < y):
      """合并(将两个有序的列表合并成一个有序的列表)"""
      items = []
      index1, index2 = 0, 0
      while index1 < len(items1) and index2 < len(items2):
          if comp(items1[index1], items2[index2]):
              items.append(items1[index1])
              index1 += 1
          else:
              items.append(items2[index2])
              index2 += 1
      items += items1[index1:]
      items += items2[index2:]
      return items


  def merge_sort(items, comp=lambda x, y: x < y):
      return _merge_sort(list(items), comp)


  def _merge_sort(items, comp):
      """归并排序"""
      if len(items) < 2:
          return items
      mid = len(items) // 2
      left = _merge_sort(items[:mid], comp)
      right = _merge_sort(items[mid:], comp)
      return merge(left, right, comp)
  ```
- 猴子排序

## 查找算法

- 顺序查找: 

  ```python
  def seq_search(items, key):
      """顺序查找"""
      for index, item in enumerate(items):
          if item == key:
              return index
      return -1
  ```
- 折半查找: 

  ```python
  def bin_search(items, key):
      """折半查找"""
      start, end = 0, len(items) - 1
      while start <= end:
          mid = (start + end) // 2
          if key > items[mid]:
              start = mid + 1
          elif key < items[mid]:
              end = mid - 1
          else:
              return mid
      return -1
  ```

## 算法设计

- 穷举法: 暴力破解法, 对所有的可能性进行验证, 直到找到正确答案

  - 百钱百鸡问题:

    ```python
    # 公鸡5元一只 母鸡3元一只 小鸡1元三只
    # 用100元买100只鸡 问公鸡/母鸡/小鸡各多少只
    for x in range(20):
        for y in range(33):
            z = 100 - x - y
            if 5 * x + 3 * y + z // 3 == 100 and z % 3 == 0:
                print(x, y, z)
    ```
  - 五人分鱼问题: 

    ```python
    # A、B、C、D、E五人在某天夜里合伙捕鱼 最后疲惫不堪各自睡觉
    # 第二天A第一个醒来 他将鱼分为5份 扔掉多余的1条 拿走自己的一份
    # B第二个醒来 也将鱼分为5份 扔掉多余的1条 拿走自己的一份
    # 然后C、D、E依次醒来也按同样的方式分鱼 问他们至少捕了多少条鱼
    fish = 6
    while True:
        total = fish
        enough = True
        for _ in range(5):
            if (total - 1) % 5 == 0:
                total = (total - 1) // 5 * 4
            else:
                enough = False
                break
        if enough:
            print(fish)
            break
        fish += 5
    ```
- 贪婪法: 在对问题求解时, 总是做出在当前看来最好的选择, 不追求最优解, 快速找到满意解

  - 贪婪小偷问题: 

    |名称|价格(美元)|重量(kg)|
    | --------| ------------| ----------|
    |电脑|200|20|
    |收音机|20|4|
    |钟|175|10|
    |花瓶|50|2|
    |书|10|1|
    |油画|90|9|

    ```python
    class Thing(object):
        """物品"""
    
        def __init__(self, name, price, weight):
            self.name = name
            self.price = price
            self.weight = weight
    
        @property
        def value(self):
            """价格重量比"""
            return self.price / self.weight


    def input_thing():
        """输入物品信息"""
        name_str, price_str, weight_str = input().split()
        return name_str, int(price_str), int(weight_str)


    def main():
        """主函数"""
        max_weight, num_of_things = map(int, input().split())
        all_things = []
        for _ in range(num_of_things):
            all_things.append(Thing(*input_thing()))
        all_things.sort(key=lambda x: x.value, reverse=True)
        total_weight = 0
        total_price = 0
        for thing in all_things:
            if total_weight + thing.weight <= max_weight:
                print(f'小偷拿走了{thing.name}')
                total_weight += thing.weight
                total_price += thing.price
        print(f'总价值: {total_price}美元')


    if __name__ == '__main__':
        main()
    ```
- 分治法: 把一个复杂的问题分成两个或更多的相同或相似的子问题, 再把子问题分成更小的子问题, 直到可以直接求解的程度, 最后将子问题的解进行合并得到原问题的解

  - 快速排序
- 回溯法: 试探法, 按选优条件向前搜索, 当搜索到某一步发现原先选择并不优或达不到目标时, 就退回一步重新选择

  - 骑士巡逻问题: 

    ```python
    import sys
    import time
    
    SIZE = 5
    total = 0


    def print_board(board):
        for row in board:
            for col in row:
                print(str(col).center(4), end='')
            print()


    def patrol(board, row, col, step=1):
        if row >= 0 and row < SIZE and \
            col >= 0 and col < SIZE and \
            board[row][col] == 0:
            board[row][col] = step
            if step == SIZE * SIZE:
                global total
                total += 1
                print(f'第{total}种走法: ')
                print_board(board)
            patrol(board, row - 2, col - 1, step + 1)
            patrol(board, row - 1, col - 2, step + 1)
            patrol(board, row + 1, col - 2, step + 1)
            patrol(board, row + 2, col - 1, step + 1)
            patrol(board, row + 2, col + 1, step + 1)
            patrol(board, row + 1, col + 2, step + 1)
            patrol(board, row - 1, col + 2, step + 1)
            patrol(board, row - 2, col + 1, step + 1)
            board[row][col] = 0


    def main():
        board = [[0] * SIZE for _ in range(SIZE)]
        patrol(board, SIZE - 1, SIZE - 1)


    if __name__ == '__main__':
        main()
    ```
- 动态规划: 将待求解问题分解成若干个子问题, 先求解并保存这些子问题的解, 避免产生大量的重复运算

  - 子列表元素之和的最大值: 子列表指的是列表中索引连续的元素构成的列表, 元素是int类型, 可能包含正整数、0、负整数

    ```python
    # 二重循环性能差, 动态规划性能好
    def main():
        items = list(map(int, input().split()))
        overall = partial = items[0]
        for i in range(1, len(items)):
            partial = max(items[i], partial + items[i]) 	# 当前最大值
            overall = max(partial, overall)		# 全局最大值
        print(overall)


    if __name__ == '__main__':
        main()
    ```

‍

---

‍

# 十、虚拟环境

一个隔离的Python运行环境, 允许在不影响全局Python安装的情况下, 为不同的项目创建独立的环境, 每个虚拟环境都可以有自己独立的 Python 解释器和依赖库集合

## 目的

1. 隔离项目依赖: 确保一个项目的依赖不会影响其他项目
2. 避免污染全局环境: 在系统的全局 Python 环境中安装库, 会影响所有项目, 使用虚拟环境可以将项目的依赖限定在项目目录中, 避免全局污染
3. 便于迁移和部署: 虚拟环境可以轻松生成项目的依赖清单(`requirements.txt`​), 使得在其他设备上复现项目环境变得简单
4. 测试不同版本的库: 通过虚拟环境, 可以轻松测试项目在不同版本的库或 Python 解释器下的运行情况

## **工作原理**

1. 独立的 Python 解释器: 为项目创建独立Python解释器, 避免与系统全局Python冲突
2. 独立的库目录: 虚拟环境有一独立`site-packages`​目录, 用于存放当前环境的依赖包
3. 环境激活和管理: 激活虚拟环境后, 所有的Python操作(如安装依赖)都仅限于该环境

## **创建和使用**

1. 使用 `venv`​ 模块

    - 创建虚拟环境: 

      ```python
      python -m venv myenv     # 这将在当前目录创建一个名为myenv的虚拟环境
      ```
    - 激活虚拟环境: Linux/macOS: 

      ```python
      source myenv/bin/activate 		# 激活后, 命令行会显示虚拟环境名称, 例如 (myenv)
      ```
    - 安装依赖: 在激活的虚拟环境中运行: 

      ```python
      pip install requests
      ```
    - 退出虚拟环境: 

      ```python
      deactivate
      ```

2. 记录和恢复依赖: 

    - **生成依赖清单**: 

      ```python
      pip freeze > requirements.txt
      ```
    - 从依赖清单安装: 

      ```python
      pip install -r requirements.txt
      ```

3. 使用`virtualenv`​模块

    功能更强大的第三方工具, 适用于需要支持更多 Python 版本的场景

    - 安装 `virtualenv`: 

      ```python
      pip install virtualenv
      ```

      使用方式类似于 `venv`​

## **特点**

1. 独立性: 每个虚拟环境之间相互独立, 不会共享库或配置
2. 轻量级: 虚拟环境仅复制必要的文件, 尽量减少磁盘占用
3. 多版本支持: 可以同时管理多个 Python 版本或库版本

‍

---

‍

# 十一、图形界面

Graphical User Interface, GUI, 图形用户界面, 由**窗口**和**控件**组成

![截屏2024-12-25 19.53.10](../assets/截屏2024-12-25%2019.53.10-20241225195316-z153pf0.png)​

- Panel面板: 一个没有标题栏的容器, 可容纳其他控件的控件
- StaticText静态文本

## 构建wxPython程序

需要两个对象: 

- 一个应用对象: wx.App
- 一个窗口对象:wx.Frame

```python
import wx

app = wx.App()			 # 创建应用程序对象
frm = wx.Frame(None, title='', size=(), pos=()) 	# 创建窗口对象, 一参为所在父窗口

frm.Show() 		# 显示窗口, 默认隐藏
app.MainLoop() 	# 让应用程序进入主事件循环中
```

## 自定义窗口类: 

```python
class MyFrame(wx.Frame):
	def __init__(self):
		super().__init__(None, title='第一个wxPython程序', size=(400, 300), pos=(100, 100))
		# 窗口控件代码
```

## 控件

```python
class MyFrame(wx.Frame):
	def __init__(self):
		super().__init__(None, title='第一个wxPython程序', size=(400, 300), pos=(100, 100))

		# 创建面板对象, parent=self即设面板所在父容器为当前窗口对象
		panel = wx.Panel(parent=self)

		# 创建静态文本对象, 并放到panel面板中, label为显示文字, pos设置文本位置
		statictext = wx.StaticText(parent=panel, label='Hello World!', pos=(10, 10))
```

继承自`wx.Control类`​

- 文本输入控件

  - 普通文本输入控件 (只能输入单行文本): `tc1 = wx.TextCtrl(panel)`​
  - 多行文本输入控件: `tc2 = wx.TextCtrl(panel, style=wx.TE_MULTILINE)`​
  - 密码输入控件: `tc2 = wx.TextCtrl(panel, style=wx.TE_PASSWORD)`​
- 单选按钮、复选框

  ![截屏2024-12-25 20.58.30](../assets/截屏2024-12-25%2020.58.30-20241225205833-6805nv0.png)

  - 单选控件: `wx.RadioButton`​, 同一组的多个单选按钮互斥, 一个按下, 其他必释放
  - 多选控件: `wx.CheckBox`​, 可单独使用, 能提供两种状态的开和关

  ```python
  class MyFrame(wx.Frame):
  	def __init__(self):
  		super().__init__(None, title='单选按钮和复选框', size=(400, 300))
  		panel = wx.Panel(parent=self)
  
  		st1 = wx.StaticText(panel, label='选择你最喜欢的编程语言: ')
  		cb1 = wx.CheckBox(panel, id=1, label='Python') 	# 创建单选按钮
  		cb2 = wx.CheckBox(panel, id=2, label='Java')	# 创建单选按钮
  		cb2.SetValue(True) 		# cb2初始状态为选中
  		cb3 = wx.CheckBox(panel, id=3, label='C++')		# 创建单选按钮
  		# 绑定id为1～3的所有控件的事件处理到on_checkbox_click()方法
  		self.Bind(wx.EVT_CHECKBOX, self.on_checkbox_click, id=1, id2=3)
  
  		st2 = wx.StaticText(panel, label='选择性别: ')
  		# 设置style=wx.RB_GROUP的按钮为一个组的开始, 直到遇到另一个设wx.RB_GROUP的按钮为止均为同一组
  		radio1 = wx.RadioButton(panel, id=4, label='男', style=wx.RB_GROUP)  # 创建单选按钮
  		radio2 = wx.RadioButton(panel, id=5, label='女')	# 创建单选按钮
  		# 绑定id从4~5的控件到on_radio1_click()方法
  		self.Bind(wx.EVT_RADIOBUTTON, self.on_radio1_click, id=4, id2=5)
  
  		hbox1 = wx.BoxSizer()
  		hbox1.Add(st1, flag=wx.LEFT|wx.RIGHT, border=5) 
  		hbox1.Add(cb1)
  		hbox1.Add(cb2)
  		hbox1.Add(cb3)
  
  		hbox2 = wx.BoxSizer()
  		hbox2.Add(st2, flag=wx.LEFT|wx.RIGHT, border=5) 
  		hbox2.Add(radio1)
  		hbox2.Add(radio2)
  
  		vbox = wx.BoxSizer(wx.VERTICAL)
  		vbox.Add(hbox1, flag=wx.ALL, border=10) 
  		vbox.Add(hbox2, flag=wx.ALL, border=10)
  
  		#设置面板(panel)采用vbox布局管理器
  		panel.SetSizer(vbox)
  从事件对象中取出事件源对象(复选框)
  
  	def on_checkbox_click(self, event):
  		cb = event.GetEventObject()
  		# 从事件对象中取出事件源对象(复选框)
  		print('选择｛0｝, 状态｛1｝'.format(cb.GetLabel(), event.IsChecked())) # 获得复选框
  
  	def on_radio1_click(self, event):
  		rb = event.GetEventObject() 	# 事件对象中取出事件源对象(单选按钮)
  		print('第一组｛0｝个被选中'.format(rb.GetLabel()))
  ```
- 列表控件: 可单选/多选, `wx.ListBox类`​

  ![截屏2024-12-25 21.20.07](../assets/截屏2024-12-25%2021.20.07-20241225212010-43jv7l1.png)

  - style参数: 

    - `wx.LB_SINGLE`: 单选
    - `wx.LB_MULTIPLE`: 多选
    - `wx.LB_EXTENDED`: 多选, 需要在按住Ctrl或Shift时选择项目
    - `wx.LB_SORT`: 对列表选择项进行排序

    ```python
    class MyFrame(wx.Frame):
    	def __init__(self):
    		super().__init__(None, title='列表', size=(400, 300))
    		panel = wx.Panel(parent=self)
    
    		st1 = wx.StaticText(panel, label='选择你喜欢的编程语言: ')
    		list1 = ['Python', 'C++', 'Java']
    
    		# 创建列表控件, choices设置列表选项, style设置列表风格
    		lb1 = wx.ListBox(panel, choices=list1, style=wx.LB_SINGLE)
    		self.Bind(wx.EVT_LISTBOX, self.on_listbox1, lb1)
    
    		st2 = wX.StaticText(panel, label='选择你喜欢吃的水果: ')
    		list2 =［'苹果', '橘子', '香蕉'］
    		lb2 = wx.ListBox(panel, choices=list2, style=wx.LB_EXTENDED)
    		self.Bind(wx.EVT_LISTBOX, self.on_listbox2, lb2)
    
    		hbox1 = wx.BoxSizer()
    		hbox1.Add(st1, proportion=1, flag=wx.LEFT|wx.RIGHT, border=5) 
    		hbox1.Add(lb1, proportion=1)
    
    		hbox2 = wx.BoxSizer()
    		hbox2.Add(st2, proportion=1, flag=wx.LEFT|wx.RIGHT, border=5) 
    		hbox2.Add(lb2, proportion=1)
    
    		vbox = wx.BoxSizer(wx.VERTICAL)
    		vbox.Add(hbox1, flag=wx.ALL|wx.EXPAND, border=5) 
    		vbox.Add(hbox2, flag=wx.ALL|wx.EXPAND, border=5)
    
    		panel.SetSizer(vbox)
    
    	def on_listbox1(self, event):
    		listbox = event.GetEventObject()
    		print('选择{0}'.format(listbox.GetSelection())) # 返回单个选中项目的索引序号
    
    	def on_listbox2(self, event):
    		listbox = event.GetEventObject()
    		print('选择{0}'.format(listbox.GetSelections())) # 返回多个选中项目的索引序号列表
    ```
- 静态图片控件: 用于显示图片, `wx.StaticBitmap类`​

  最后图片替换后, 需重写绘制窗口, 否则布局会混乱

  ![截屏2024-12-25 21.37.25](../assets/截屏2024-12-25%2021.37.25-20241225213728-vchzqxu.png)

  ```python
  class MyFrame(wx.Frame):
  	def __init__(self):
  		super().__init__(None, title='静态图片控件', size=(300, 300))
  		panel = wx.Panel(parent=self)
  
  		# 创建wx.Bitmap图片对象的列表
  		self.bmps = [wx.Bitmap('images/bird5.gif', wx.BITMAP_TYPE_GIF),
  					 wx.Bitmap('images/bird4.gif', wx.BITMAP_TYPE_GIF),
  					 wx.Bitmap('images/bird3.gif', wx.BITMAP_TYPE_GIF)]
  
  		b1 = wx.Button(self.panel, id=1, label='Button1')
  		b2 = wx.Button(self.panel, id=2, label='Button2')
  		self.Bind(wx.EVT_BUTTON, self.on_click, id=1, id2=2)
  
  		# 静态图片控件对象, self.bmps[0]是静态图片控件要显示的图片对象
  		self.image = wx.StaticBitmap(self.panel, bitmap=self.bmps[0])
  
  		#创建垂直方向的布局管理器对象vbox
  		vbox = wx.BoxSizer(wx.VERTICAL)
  		# 添加标控件到布局管理器对象vbox
  		vbox.Add(b1, proportion=1, flag=wx.EXPAND) 
  		vbox.Add(b2, proportion=1, flag=wx.EXPAND) 
  		vbox.Add(self.image, proportion=3, flag=wx.EXPAND)
  
  		self.panel.SetSizer(vbox)
  
  	def on_click(self, event):
  		event_id = event.GetId()
  		if event_id == 1:
  			self.image.SetBitmap(self.bmps[1]) # 重新设置图片, 实现图片切换
  		else:
  			self.image.SetBitmap(self.bmps[2])
  		self.panel.Layout() 	# 重新设置panel面板布局
  ```

‍

## 事件处理

![截屏2024-12-25 20.01.13](../assets/截屏2024-12-25%2020.01.13-20241225200115-9yz75ut.png)​

事件源: 事件发生的场所, 即各个控件, 如按钮事件的事件源就是按钮

事件: wxPython中的事件被封装为事件类`wx.Event`​及其子类

- 按钮事件类为`wx.CommandEvent`​
- 鼠标事件类为`wx.MoveEvent`​

事件处理程序: 一个响应用户事件的方法

```python
class MyFrame(wx.Frame):
	def __init__(self):
		super().__init__(None, title='事件处理', size=(400, 300))
		panel = wx.Panel(parent=self)
		self.statictext = wx.StaticText(parent=panel, label='请单击OK按钮', pos=(110, 20))

		# 创建按钮对象
		b = wx.Button(parent=panel, label='OK', pos=(100, 50))

		# 绑定事件, 一参为事件类型, 二参为事件处理程序, 三参为事件源
		self.Bind(wx.EVT_BUTTON, self.on_click, b)

	# 事件处理程序
	def on_click(self, event):
		self.statictext.SetLabelText('Hello World!')
```

## 布局管理

1. 绝对布局: 控件的位置和大小使用绝对数值, 控件不随窗口的变化而变化(尽量不用)
2. 布局管理器: 

    - 盒子布局管理器: `wx.BoxSizer类`​, 最常用, 可让子窗口/控件沿垂直/水平方向布局

      ![截屏2024-12-25 20.11.25](../assets/截屏2024-12-25%2020.11.25-20241225201127-fuv7k6y.png)

      - 构造方法

        ```python
        wx.BoxSizer(wx.HORIZONTAL)		# 水平方向布局, 默认, 可省略
        wx.BoxSizer(wx.VERTICAL)		# 垂直方向布局
        ```
      - `Add()`​方法: 添加子窗口/控件到父窗口

        - `Add(window, proportion=0, flag=0, border=0)`: 添加到父窗口

          - proportion: 用于设置当前子窗口/控件在父窗口所占空间比例
          - flag: 布局标志, 用来控制对齐方式、边框和调整尺寸

            - 对齐标志

              ![截屏2024-12-25 20.18.36](../assets/截屏2024-12-25%2020.18.36-20241225201839-3kxaifk.png)
            - 边框标志

              ![截屏2024-12-25 20.19.03](../assets/截屏2024-12-25%2020.19.03-20241225201905-moc5x9t.png)
            - 调整尺寸标志

              ![截屏2024-12-25 20.19.29](../assets/截屏2024-12-25%2020.19.29-20241225201931-uf2ez33.png)​
          - border: 用于设置边框的宽度
        - `Add(sizer, proportion=0, flag=0, border=0)`: 添加到另外一个布局对象, 用于布局嵌套
      - 重构事件处理示例

        ![截屏2024-12-25 20.29.57](../assets/截屏2024-12-25%2020.29.57-20241225203000-dfd8e47.png)

        ```python
        class MyFrame(wx.Frame):
        	def __init__(self):
        		super().__init__(None, title='事件处理', size=(400, 300))
        		panel = wx.Panel(parent=self)
        		self.statictext = wx.StaticText(parent=panel, label='请单击OK按钮', pos=(110, 20))
        		b = wx.Button(parent=panel, label='OK')
        		self.Bind(wx.EVT_BUTTON, self.on_click, b)
        	
        		# 布局相关代码
        		# 创建垂直方向的盒子布局管理器对象vbox
        		vbox = wx.BoxSizer(wx.VERTICAL)
        
        		# 添加静态文本到vbox布局管理器
        		vbox.Add(
        			self.statictext, 
        			proportion=1, 		# 两个控件proportion都为1, 故各占一半
        			flag=wx.ALIGN_CENTER_HORIZONTAL|wx.FIXED_MINSIZE|wx.TOP,  # 控件水平居中, 顶部有边框, 宽度为30
        			border=30
        		)
        
        		# 添加按钮b到vbox布局管理器
        		vbox.Add(b, proportion=1, flag=wx.EXPAND|wx.BOTTOM, border=10) 	# 完全填满
        
        		# 设置面板panel采用vbox布局管理器
        		panel.SetSizer(vbox)
        
        	def on_click(self, event):	
        		self.statictext.SetLabelText('Hello World!')
        ```
      - 嵌套示例

        ![截屏2024-12-25 20.55.06](../assets/截屏2024-12-25%2020.55.06-20241225205509-ybb2te4.png)

        ```python
        class MyFrame(wx.Frame):
        	def __init__(self):
        		super().__init__(None, title='布局管理器嵌套', size=(300, 120))
        		panel = wx.Panel(parent=self)
        		self.statictext = wx.StaticText(parent=panel, label='请单击按钮')
        		b1 = wx.Button(parent=panel, id=10, label='Button1')
        		b2 = wx.Button(parent=panel, id=11, label='Button2')
        
        		# 创建水平方向的盒子布局管理器hbox对象
        		hbox = wx.BoxSizer(wx.HORIZONTAL)
        		# 添加b1到hbox布局管理
        		hbox.Add(b1, proportion=1, flag=wx.EXPAND|wx.ALL, border=10)
        		# 添加b2到hbox布局管理
        		hbox.Add(b2, proportion=1, flag=wx.EXPAND|wx.ALL, border=10)
        
        		# 创建垂直方向的盒子布局管理器vbox对象
        		vbox = wx.BoxSizer(wx.VERTICAL)
        		# 添加静态文本到vbox布局管理器
        		vbox.Add(self.statictext, proportion=1, flag=wx.CENTER|wx.FIXED_MINSIZE|wx.TOP, border=10)
        		# 将水平hbox布局管理器对象嵌入到垂直vbox布局管理器对象
        		vbox.Add(hbox, proportion=1, flag=wx.CENTER)
        
        		# 设置面板panel采用vbox布局管理器
        		panel.SetSizer(vbox)
        
        		# 将两按钮b1、b2的单击事件绑定到self.on_click方法
        		self.Bind(wx.EVT_BUTTON, self.on_click, id=10, id2=20)
        
        	def on_click(self, event):
        		event_id = event.GetId()	# 获得绑定按钮的id
        		print(event_id)
        		if event_id == 10:		# 根据id判断单击了哪个按钮
        			self.statictext.SetLabelText('Button1单击')
        		else:
        			self.statictext.SetLabelText('Button2单击')
        ```
    - 网格布局管理器
