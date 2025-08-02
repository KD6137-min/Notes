# 数据类型

Python为动态语言, 变量类型不用声明, 由值决定, 不同类型存储机制不同

整型8字节, 字符1字节

序列: 可迭代, 元素有序的容器, 如字节序列bytes、str、list、tuple

引用类型/可变类型: list、dict、set

基本数据类型/不可变类型: int、str、tuple

## 整数

实现原理：整数对象在Python内部用`PyIntObject`​结构体表示: 

```c
typedef struct {
    PyObject_HEAD
    long ob_ival;
} PyIntObject;
```

PyObject\_HEAD宏中定义的两个属性分别是: 

```c
int ob_refcnt;    
struct _typeobject *ob_type;
```

这两个属性是所有Python对象固有的: 

- ob\_refcnt: 对象的引用计数
- ob\_type: 用于描述Python对象的类型信息

### 小整数对象池small_ints

为优化性能, 缓存[-5, 257)之间整数

```c
#define NSMALLPOSINTS           257
#define NSMALLNEGINTS           5
static PyIntObject *small_ints[NSMALLNEGINTS + NSMALLPOSINTS];
```

### 大整数缓冲池PyIntBlock

避免不断的malloc分配内存带来的效率损耗

```c
struct _intblock {
    struct _intblock *next;		// 指向下一个PyIntBlock对象
    PyIntObject objects[N_INTOBJECTS]; 	// PyIntObject数组(后转为单链表), 实际存储PyIntObject对象
};
typedef struct _intblock PyIntBlock;

static PyIntBlock *block_list = NULL;

// 所有PyIntBlock内存块中空闲的内存, 存储新PyIntObject对象时, 可快速获取所需内存
static PyIntObject *free_list = NULL; 
```

这些内存块(PyIntBlock)通过一个单向链表组织在一起, 表头是`block_list`​, 表头始终指向最新创建的PyIntBlock对象

### 创建过程

创建一个整数对象时, 如果它在小整数范围内, 就直接从小整数缓冲池中直接返回, 如果不在该范围内, 就开辟一个大整数缓冲池内存空间: 

```c
// intobject.c
PyObject* PyInt_FromLong(long ival)
{
     register PyIntObject *v; 

     //[1]尝试使用小整数对象池
     if (-NSMALLNEGINTS <= ival && ival < NSMALLPOSINTS) {
        v = small_ints[ival + NSMALLNEGINTS];
        Py_INCREF(v);
        return (PyObject *) v;
    }

    //[2]为通用整数对象池申请新的内存空间
    if (free_list == NULL) {
        if ((free_list = fill_free_list()) == NULL)
            return NULL;
    }
    //[3](inline)内联PyObject_New的行为
    v = free_list;
    free_list = (PyIntObject *)v->ob_type;
    PyObject_INIT(v, &PyInt_Type);
    v->ob_ival = ival;
    return (PyObject *) v;
}
```

fill_free_list()返回一个`free_list`​链表

当整数对象ival创建成功后, `free_list`​表头就指向了`v->ob_type`​(理解为指向下一个PyIntObject的指针)

```c
// intobject.c
static PyIntObject* fill_free_list(void)
{
    PyIntObject *p, *q;
    // 申请大小为sizeof(PyIntBlock)的内存空间
    // block_list始终指向最新创建的PyIntBlock
    p = (PyIntObject *) PyMem_MALLOC(sizeof(PyIntBlock));
    ((PyIntBlock *)p)->next = block_list;
    block_list = (PyIntBlock *)p;

    // 将PyIntBlock中的PyIntObject数组(objects)转变成单向链表
    p = &((PyIntBlock *)p)->objects[0];
    q = p + N_INTOBJECTS;
    while (--q > p)
        // ob_type指向下一个未被使用的PyIntObject, 
        q->ob_type = (struct _typeobject *)(q-1);
    q->ob_type = NULL;
    return p + N_INTOBJECTS - 1;
}
```

PyIntObject对象销毁时, 它所占用的内存并不会释放, 而是继续被Python使用, 进而将`free_list`​表头指向了这个要被销毁的对象上: 

```c
// intobject.c
static void int_dealloc(PyIntObject *v)
{
    if (PyInt_CheckExact(v)) {
        v->ob_type = (struct _typeobject *)free_list;
        free_list = v;
    }
    else
        v->ob_type->tp_free((PyObject *)v);
}
```

## 列表

有序可变, 类型可不同, 可嵌套, 可变: 内存地址不变, 数据可变

堆中存储的列表是列表的指针, 列表类型变量：

- 赋值时, 指向堆中同一地址
- 浅复制时, 只复制基本类型, 元素为指针的, 实际上仍为同一元素
- 深复制时, 复制所有元素拷贝

## 字典

底层为高度优化的哈希表结构，有序（官方保持插入顺序），无链表，使用开放寻址（线性探测）

- 内部结构：

    - `ma_keys`​：存储键keys和哈希值
    - `ma_values`​：存储值values
    - `entries`​数组：用于查找和维护key的索引信息
- 核心思想：引入新数据结构`compact dict`​，使用两个数组，一个保存key-hash-value对，一个保存索引顺序（即插入顺序）
- 相比`OrderedDict`​：dict适合常规使用，性能好，OrderedDict支持更多功能（如移动元素）

## 集合

唯一性, 元素不可重复, set(序列)即可去重

无序性, 无索引, 内部使用哈希表存储元素, 排列顺序与添加顺序无关

可变性, 增减元素, 内存地址不变

## 迭代器

只能单向取数, 数据取完即**关闭消失**​, 可迭代协议`__iter__()`​、`__next__()`​

- 迭代器一定是可迭代对象, 可迭代对象不一定是迭代器, 迭代器包含生成器
- list、str、tuple、dict、set不是迭代器, 是可迭代对象
- 迭代器是惰性对象, 不会一次性加载所有数据, 有自己内部"状态", 会记录当前遍历到的位置, 按需生成下一个元素

## 生成器

特殊的迭代器, 懒加载, 按需产生数据, 同一时间内存中只存在一个数据, 节省内存, 惰性求值适合生成无限序列

`函数+yield()`: 暂停函数并产出一个数据, 调用不会进入函数, 而是产生一个生成器对象, 当获取生成器中数据时才进入函数, 执行函数中的内容直到yield位置, 暂停并把产生的数据返回, 下一次再获取数据, 从上次暂停的位置继续执行到yield位置, 并返回生成的数据‍



---



# 变量与作用域

变量存储的是对象的**引用**(在栈中), 实际是一个字符串符号, 用来关联存储在内存中的对象

赋值语句就是建立变量与对象的映射关系，**引用赋值**​, 创建一个新的引用, 即传递原对象内存地址给左侧, 实际上未创建新对象

```python
import copy
a = [0, 1, [2, 3]]
b = a
b[2][0] = 99
print(a)	// [0, 1, [99, 3]]
print(b)	// [0, 1, [99, 3]] // 简单赋值=传递引用, 指向同一对象
```

显式删除变脸：`del var_name`

## 动态类型

变量仅为符号(字符串对象), 本身并不存储类型信息, 类型信息存储在对象上, 两操作数执行运算时必须先检查两个操作数的类型

> Python对象机制的核心: 类型信息和引用计数

`type(a)`：输出a的类型

## 命名空间

一个**容器**, 存储标识符与对应对象之间的映射关系, 解决命名冲突问题

- 由一个`dict`​实现, 维护`(name, obj)`关联关系, 可以一个套一个地形成一条**命名空间链**, Python执行时大部分时间消耗在从命名空间链中确定符号对应的对象
- 类、函数、module都对应着一个独立的命名空间
- 一个独立的命名空间会对应一个`PyCodeObject`​对象
- import foo时创建一个新变量foo, 绑定一个PyCodeObject对象, 即foo.py的编译结果
- `dir()`​函数, 查看一个命名空间下的所有名字符号

## 作用域

描述命名空间中标识符的可见性和生命周期, 用于避免作用范围外的代码访问变量

- 分支语句和循环中的变量不存在作用域问题, 外部可直接访问
- `LEGB`​规则: 基于作用域的命名空间查找规则, Python使用LEGB顺序查找符号对应的对象, `locals`​->`enclosing function`​->`globals`​->`builtins`​
    - `locals`: 局部命名空间, 存储函数或方法内部定义的局部变量、函数的参数, 外部不能访问
        - 函数内外出现同名变量, 函数内使用函数内变量
        - 若函数中无此变量, 则在全局变量中查找
        - 函数内定义变量名与函数外变量同名, 不改变函数外变量值
        - `locals()`: 查看所有函数内部的局部变量
    - `enclosing`: 外部嵌套函数的命名空间(闭包中常见), 将变量定义在外函数内, 内函数外
    - `globals`: 全局命名空间, 存储模块级的变量、函数、类等, 任意位置访问变量, 限于模块内
    - `builtins`: 内置命名空间, Python启动时自动载入, 如dict、list、type、print, 均位于`__builtins__`​模块中



---



# 函数

一等公民, 封装功能, 按需调用, 可嵌套, 简洁易维护, 属于`function类`

## 一等公民

指可作为函数参数、函数返回值、赋值给变量、存储在数据结构中、传递和操作的对象, 即可像任何其他类型的数据一样被使用和操作的实体

Python中的'一等公民'：函数、类、方法、对象

## 原理

`def`​实际上是执行一条指令, 用来创建函数, 函数是执行到的时候才创建, 先创建一个**函数对象**, 然后将func这个名称符号绑定到这个函数上

```python
# def func()的字节码
30 LOAD_CONST               5 (<code object func at 00D97650, file "demo.py", line 6>)
33 MAKE_FUNCTION            0
36 STORE_NAME               2 (func)
```

Python无法实现C和Java中的重载, 用def语句再次创建一个同名的函数时, 这个变量名就绑定到新的函数对象上

## 闭包

closure，指**一个函数**和**其周围的自由变量的引用**组合而成的对象, 可让函数'记住'它定义时的环境, 即使函数被调用时这些环境已离开作用域

**特点：**

- 函数嵌套函数
- 自由变量: 内层函数引用了外层函数中的变量
- 持久化环境: 即使外层函数执行完毕, 闭包仍然保留对自由变量的访问权

**结构：**

- **外层函数**: 提供了定义和变量的作用域
- **内层函数**: 引用了外层函数中的变量, 并作为返回值存在

:vs:**闭包和普通函数的区别：**

| **普通函数**                 | **闭包**                                         |
| ---------------------------- | ------------------------------------------------ |
| 没有'记住'外部变量的能力     | 能够记住外部函数的变量, 即使外部函数已经执行完毕 |
| 作用域受限于当前的执行环境   | 闭包保存了自由变量的引用, 可以在不同作用域中使用 |
| 变量的生命周期受限于函数调用 | 变量的生命周期可以延长, 直到闭包不再被引用       |

:warning:**注意:**

- `nonlocal`​关键字: 如果需要在闭包中修改外层函数的变量, 必须使用`nonlocal`​声明
- 可能导致内存泄漏: 如果闭包持有过多不必要的引用, 可能会导致内存无法释放
- 避免过度依赖闭包: 虽然闭包非常强大, 但在某些场景下可能会导致代码可读性下降

## 装饰器

在不改变原函数代码的情况下, 动态地为函数或方法添加功能(可复用, 可多个修饰一个函数)

**原则:** 封闭开放, 定义好的功能不再修改, 增加额外的功能

**本质:** 是一个函数, 它接受另一个函数作为参数, 对其进行扩展或修改后返回一个新的函数

**优点:**

- 代码复用: 通过装饰器, 可以为多个函数动态添加相同的功能
- 分离逻辑: 把通用功能(如日志、权限验证)与业务逻辑分离
- 简洁优雅: 避免重复代码, 提高代码可读性

- 若有多个装饰器, 从上到下执行, 原函数只调用一次



---



# 文件操作

文件类型：

- 文本文件: 以字符形式存储数据, 字符有编码
- 二进制文件: 以字节形式存储数据, 无编码, 如exe, jpg, png, word, excel, ppt等

文件路径：

| 路径表达式 | 含义                                     |
| ---------- | ---------------------------------------- |
| `.`​        | 文件所在的当前文件夹                     |
| `..`​       | 上一级文件夹                             |
| `/`​        | 从根节点选取(Mac为正斜`/`​, win为反斜`\`​) |

- 绝对路径: 从盘开始到具体文件结束
- 相对路径：

    - `./`为正在编辑的文件所在的当前文件夹
    - `../`为正在编辑的文件所在文件夹的上级文件夹



---



# 内存管理

## 内存结构

程序使用内存的划分(逻辑, 按地址从高到低):

- 栈`Stack`: 存储局部、临时变量, 及函数调用时保存现场和恢复现场需要用到的数据(栈帧), 代码块开始执行时自动分配, 代码块执行结束时自动释放, 通常由编译器自动管理
- 静态区`Static Pool`: 存储全局变量、静态变量, 程序结束释放
- 只读数据段`Read-Only Data Segment`: 常量
- 代码段`Code Segment`: 存储程序的机器码、Python字节码文件、模块等
- 方法区`Method Area`: 存储类定义、方法定义、模块内容、元数据等
- 堆`Heap`: 动态分配, 存储引用类型的对象, 垃圾回收机制自动清理内存

**:vs:Python和C++堆栈使用的区别:**

- C++: 局部变量直接在栈中分配, 自动释放，使用`new`​的在堆中分配, 需要手动释放

- Python:

    - 栈中变量为对象的引用: 引用是高级抽象, 本质是通过变量名指向堆中的对象, 表示一个变量与对象的关联, 不直接表示内存地址, 而是指向某个对象的标识符, 可理解为对指针的封装, 使得我们通过变量名访问对象, 而不需要直接操作内存地址
    - 堆中存储所有对象, 无论对象是可变类型还是不可变类型
    - 通过引用计数和垃圾回收机制自动管理内存

## 垃圾回收机制

GC，Garbage Collection, 在`引用计数机制`​基础上, 引入`标记—清除`​和`分代收集`​技术解决循环引用问题

```c
typedef struct _object {
    /* 引用计数 */
    int ob_refcnt;
    /* 对象指针 */
    struct _typeobject *ob_type;
} PyObject;

/* 增加引用计数的宏定义 */
#define Py_INCREF(op)   ((op)->ob_refcnt++)
/* 减少引用计数的宏定义 */
#define Py_DECREF(op) \ //减少计数
    if (--(op)->ob_refcnt != 0) \
        ; \
    else \
        __Py_Dealloc((PyObject *)(op))
```

**:star:导致引用+1的情况：**

- 对象被创建, 如`a = 23`​
- 对象被引用, 如`b = a`​
- 对象被作为参数, 传入到一个函数中, 如`f(a)`​
- 对象作为一个元素, 存储在容器中, 如`list1 = [a, a]`​

**:star:导致引用计数-1的情况：**

- 对象的别名被显式销毁, 如`del a`​
- 对象的别名被赋予新的对象, 如`a = 24`​
- 一个对象离开它的作用域, 如f函数执行完毕时, f函数中的局部变量
- 对象所在的容器被销毁, 或从容器中删除对象

**:star:解决循环引用：循环引用会导致内存泄漏**

- **弱引用**: 一种引用对象的方式, 不阻止对象被垃圾回收(GC)机制回收, 允许引用一个对象却不增加该对象的引用计数, 从而不会影响垃圾回收器对该对象的回收, 常用于缓存和引用计数管理

    - `weakref`​模块, 创建弱引用: `weak_obj = weakref.ref(obj)`​
- **标记—清除**和**分代收集**技术: 在创建一个对象的时候, 对象被放在第一代中, 若在第一代的垃圾检查中对象存活了下来, 该对象就会被放到第二代中, 同理在第二代的垃圾检查中对象存活下来, 该对象就会被放到第三代中

**:star:导致垃圾回收的情况：**

- 调用`gc.collect()`​
- `gc`​模块的计数器达到阀值
- 程序退出

## 对象池

数值类型和字符串类型不可变, 每次修改实际为创建新对象

- 小整数对象池: 避免为整数频繁申请和销毁内存空间, [-5, 257), 不包括257, 范围内整数对象提前建立好, 不会被垃圾回收, 该范围内的整数均使用同一对象

- 大整数对象池: 每创建一个大整数时, 都会新建一个对象, 该对象引用计数为0时**不销毁**, 后面再建立的对象会**复用**之前已经不再使用的对象的内存空间

- 字符串对象缓冲池: 缓存单字节的字符对应的字符串对象(PyStringObject)

    - `intern`​机制: 用于将内容相同的字符串变量转换成指向同一个字符串对象, 维护一个名为`interned`​的集合, 存储(key, value)映射关系, 记录被intern机制处理过的 PyStringObject对象, 注意: intern机制是在字符串被创建后才起作用, Python始终会为字符串创建PyStringObject对象

        ```python
        >>> a = intern('a string') # 手动调用 intern 方法
        >>> b = intern('a string')
        >>> a is b
        True
        ```

## `__slots__`​优化

限制类的实例只允许特定的属性, 减少每个对象的内存开销(不再需要存储`__dict__`​)

每个实例对象都会有一个`__dict__`​属性, 用于存储实例的所有属性

‍

---



# 模块管理

## 程序编译

- 代码开始执行后, 启动Python解释器, 将`.py`文件编译为**字节码对象`PyCodeObject`**(包含源代码中的字符串、常量值、字节码指令及字节码指令和源代码行号的对应关系)

    - `dis.dis.()`: 接收一个code对象, 输出code对象中字节码指令信息

        ```python
        import dis
        
        s = open('demo.py').read()
        co = compile(s, 'demo.py', 'exec')
        dis.dis(co)
        ```

- Python虚拟机从`PyCodeObject`对象中依次读入每一条字节码指令, 并在当前的`上下文环境`​中执行这条字节码指令

- 执行完后, 编译结果保存为.pyc文件, 下次不用再编译, 直接加载到内存

    - .pyc文件: 字节码对象在硬盘上的表现形式, 包含三部分信息:

        - Python的**magic number**: Python定义的一个用来保证Python兼容性的整数值, 不同版本的Python实现都会定义不同的magic number, 检查magic number即可限制低版本编译的pyc文件不能让高版本的Python程序来执行
        - .pyc文件创建的时间信息
        - PyCodeObject对象
    - 创建.pyc文件: `python generate_pyc.py module_name`​
    - Python只会对那些以后可能继续被使用和载入的模块才会生成pyc文件, 即被import的模块有pyc文件, 而**<u>主模块不生成pyc文件</u>**

## import

引入，将模块、函数、类或变量加载到当前的命名空间

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
    - `from module import *`: 只能导入模块中`__all__`​规定的对外公开的内容, 指定导入则无此限制
    - 使用from导入相同功能名时, 调用的是最后定义或导入的功能
    - `import module as 别名`​, 解决重名或过长问题

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
2. 若无, 则搜索shell变量`PYTHONPATH`下的每个目录
3. 若无, 则查看默认路径, UNIX下默认路径一般为`/usr/local/lib/python`                                         

## 程序结构

### 模块

Module, 一个.py文件, modules中有一个主module作为程序入口

`__name__`: 模块的内置属性, 定义当前类/模块/脚本的名称, Python程序启动后, 自动为每个模块设置`__name__`​, 一般用文件名, 主模块例外, 被设置为`'__main__'`​

建议每个.py文件都使用`if __name__ == '__main__':`​

- if之前的代码**无条件执行**, 无论是作为主模块还是被导入
- if之后的代码只有脚本作为主模块运行时执行

### 包

Package, 有联系的模块的**集合**, 与同路径下`__init__.py`​文件共同组成包

`__init__.py`**: 包中的特殊文件, 导入包时解释器**自动执行**包中`__init__.py`​文件

作用:

- 初始化包: 导包自动执行, 需执行一些初始化操作

- 标识包: 告诉解释器此文件夹为包, 而不是普通文件夹

- 控制包的导入行为:

    - 在`__init__.py`​中定义`__all__`​变量, 控制包的公共接口，若无`__all__`​变量, `from module import *`​会导入所有不以下划线开头的名称

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



---



# 虚拟环境

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

## **特点**

1. 独立性: 每个虚拟环境之间相互独立, 不会共享库或配置
2. 轻量级: 虚拟环境仅复制必要的文件, 尽量减少磁盘占用
3. 多版本支持: 可以同时管理多个 Python 版本或库版本



---



# 魔术方法

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



---



# **自省机制**

Introspection, Python对象能在运行时动态获取有关自身的信息, 如对象的类型、属性、方法等, Python动态语言特性的核心体现

## **常用自省工具**

- `type(obj)`: 返回对象的类型
- `isinstance(obj, cls_tuple)`: 检查对象是否是某个类或其子类的实例, 返回布尔值，可为单类型或类型元组
- `dir()`: 返回对象的属性和方法列表，或一个命名空间下所有名字符号
- `id()`: 返回对象的内存地址
- `hasattr(obj, attr)`: 检查对象是否具有指定属性
- `getattr(obj, attr)`: 获取对象的指定属性值
- `setattr(obj, attr, value)`: 动态设置对象的属性
- `callable()`: 检查对象是否可以被调用
- `sys.modules`: 字典，记录当前Python解释器已经加载的所有模块，键为模块名（字符串），值是模块对象

    ```python
    # 获取当前模块，可使用cur_module.xxx调用当前模块中的函数
    cur_module = sys.modules[__name__]
    ```

- inspect模块: 提供高级的自省功能, 如获取函数签名、源代码等

  ```python
  import inspect
  
  def my_function(a, b):
      return a + b
  
  print(inspect.signature(my_function))  # (a, b)
  print(inspect.getsource(my_function))  # 显示函数源代码
  ```

## `__all__`

包含**字符串**的列表, 模块级别的特殊变量，**用于声明模块的公开接口**，当使用 `from module import *` 时，只有 `__all__` 列表中指定的对象会被导入

```python
# my_module.py
__all__ = ['public_func']  # 仅公开 public_func

def public_func(): pass
def _internal_func(): pass
```

```python
from my_module import *  
public_func()  # 可用  
_internal_func()  # 报错（未导入）
```

## `__doc__`

文档字符串：Docstring，类、函数定义下被`"""`包裹的注释

```python
print(math.sqrt.__doc__)     # 查看 math.sqrt 的文档
print(pandas.DataFrame.__doc__)  # 查看类的文档
```

## `__file__`

定位模块的源代码路径，适合查看实现细节

```python
import pandas as pd
print(pd.__file__)  # 输出包路径，如 /.../pandas/__init__.py
```

## `pydoc`

命令行工具，在终端生成模块的文档或直接查看

```bash
pydoc math            # 终端查看
pydoc -w math         # 生成 math.html 文档
python -m pydoc math  # 等效于 help(math)
```
