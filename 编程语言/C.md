# C

C标准核心预处理指令

|指令名称|功能说明|示例代码|标准支持（C版本）|
| ----------| --------------------------------------| ----------| -------------------|
|​`#define`​|宏定义（文本替换）|​`#define MAX 100`​|C89/C99/C11|
|​`#undef`​|取消宏定义|​`#undef MAX`​|C89/C99/C11|
|​`#ifdef`​|条件编译（如果定义则编译）|​`#ifdef DEBUG printf("Debug");`​|C89/C99/C11|
|​`#ifndef`​|条件编译（如果未定义则编译）|​`#ifndef WIN32 ...`​|C89/C99/C11|
|​`#endif`​|结束条件编译块||C89/C99/C11|
|​`#elif`​|多分支条件编译|​`#elif OS_MAC ...`​|C89/C99/C11|
|​`#else`​|默认分支（否则）|​`#else printf("Other");`​|C89/C99/C11|
|​`#pragma`​|编译器特定指令（非标准，但广泛支持）|​`#pragma pack(push, 1)`​|扩展（GCC/Clang）|
|​`#warning`​|发出警告信息|​`#warning "Deprecated!"`​|C99/C11|
|​`#error`​|触发编译错误|​`#error "Invalid platform!"`​|C99/C11|
|​`#include`​|包含头文件|​`#include <stdio.h>`​|C89/C99/C11|
|​`#include_next`​|包含下一个匹配的头文件（GNU扩展）|​`#include_next "config.h"`​|GCC扩展|
|​`#line`​|修改源代码行号（调试用途）|​`#line 100 "custom_file.c"`​|C89/C99/C11|
|​`#ident`​|添加标识符注释（编译器特定）|​`#ident "MyProject"`​|GCC/MSVC扩展|
|​`#pragma once`​|防止头文件重复包含（非标准，但通用）|​`#pragma once`​|扩展（GCC/Clang）|

### **常见误区与细节解析**

1. \*\*`#pragma`​ 的非标准性\*\*

    - 虽然`#pragma`​不是C标准的一部分，但几乎所有编译器（GCC、Clang、MSVC）都支持，用于控制编译器优化、警告等。
    - 示例：`#pragma GCC unroll 4`​（GCC展开循环4次）
2. \*\*`#define`​ 的宏展开机制\*\*

    - **宏参数处理**：`#define SQUARE(x) ((x)*(x))`​（注意括号防止运算符优先级问题）
    - **多行宏**：使用反斜杠换行`\`​：

      ```c
      #define MACRO \
          int a = 10; \
          a *= 2;
      ```
3. **条件编译的典型场景**

    ```c
    #ifdef __linux__
        // Linux特定代码
    #elif defined(__APPLE__)
        // macOS特定代码
    #else
        #error "Unsupported OS!"
    #endif
    ```
4. \*\*`#include_next`​ 的作用\*\*

    - 用于绕过标准库中的同名头文件，直接包含用户自定义的版本：

      ```c
      #include_next <stdio.h>  // 包含用户目录下的stdio.h（而非系统目录）
      ```

---

### 🛠️ **预处理指令的执行流程**

1. **扫描源代码**：逐行读取，识别以`#`​开头的预处理指令。
2. **宏展开**：替换`#define`​宏（递归展开，直到不再匹配）。
3. **条件编译**：根据宏定义情况选择性保留代码块。
4. **文件包含**：将`#include`​的头文件内容插入当前文件。
5. **生成编译单元**：输出处理后的C代码给编译器。

---

### ⚠️ **常见错误与调试技巧**

1. **宏展开陷阱**

    - **示例问题**：

      ```c
      #define ADD(a, b) a + b
      int result = ADD(5, 10 * 2);  // 实际展开为5 + 10 * 2 → 25（预期30）
      ```
    - **修复**：增加括号确保运算顺序：

      ```c
      #define ADD(a, b) ((a) + (b))
      ```
2. **条件编译的逻辑错误**

    - **示例问题**：

      ```c
      #ifdef DEBUG
          printf("Debug mode");
      #else
          printf("Release mode");
      ```
    - 如果未定义`DEBUG`​，但代码中误写为`#ifndef DEBUG`​，则逻辑反转。
3. **头文件重复包含**

    - **解决方案**：结合`#pragma once`​和`#ifndef`​：

      ```c
      #ifndef HEADER_H
          #define HEADER_H
          // 头文件内容
      #endif
      ```

---

### 🌐 **扩展：编译器特定指令**

不同编译器提供额外的预处理指令增强功能：

- **GCC**：

  ```c
  #pragma GCC diagnostic push   // 保存警告状态
  #pragma GCC diagnostic ignored "-Wunused-variable"  // 忽略警告
  #pragma GCC diagnostic pop    // 恢复警告状态
  ```
- **Clang**：

  ```c
  #pragma clang optimize off  // 禁用优化
  ```
- **MSVC**：

  ```c
  #pragma warning(disable: 4996)  // 禁用特定警告
  ```

---

### 📜 **总结**

C语言预处理指令是开发中**高效控制代码生成**的重要工具，除了`#include`​和`#define`​，还有：

- **条件编译**（`#ifdef`​等）控制代码逻辑分支
- **宏定义**（`#define`​）实现代码复用和抽象
- **警告/错误控制**（`#warning`​、`#error`​）提升代码质量
- **编译器指令**（`#pragma`​）优化编译行为

合理使用预处理指令可以显著提升代码的可维护性和可移植性，但需避免过度使用导致代码难以理解。建议遵循 **“最小化预处理”** 原则，仅在必要时使用宏和条件编译。

‍

|特性|C 语言|C++ 语言|
| ------| --------------------------------------| ------------------------------|
|**基本类型数量**|有限（整数、浮点、字符、指针、联合）|扩展性强（新增 `bool`​、`nullptr_t`​ 等）|
|**类型修饰符**|​`signed`​/`unsigned`​、`long`​/`short`​|支持 `const`​、`volatile`​、`static`​ 等|
|**引用类型**|无|支持引用（`&`​），与指针互补|
|**布尔类型**|无（通常用 `int`​ 表示 `0`​/`1`​）|内置 `bool`​ 类型（`true`​/`false`​）|
|**空指针表示**|​`(void*)0`​ 或 `NULL`​|​`nullptr`​（C++11 起）|
|**类型安全**|弱类型系统（依赖开发者）|较强的类型检查和隐式转换控制|
|**内存管理**|手动管理（`malloc`​/`free`​）|RAII 和智能指针（`unique_ptr`​/`shared_ptr`​）|
|**面向对象支持**|无|支持类、继承、多态性|
|**泛型编程**|靠宏和函数指针|模板（`template`​）和 `auto`​ 类型推断|

---

‍

C语言的bool类型

C中没有C++中的bool类型，

现代C采用C99标准：

```c
// C99标准的bool类型
#include <stdbool.h>    // 提供bool、true、false，但与C++的<stdbool.h>不同
_Bool is_valid = true;  // typedef
```

‍

C中没有标准化的string库，只有基于char*的字符串处理函数，而非string类型

‍

赋值的副作用: 赋值过程中不仅改变了目标变量的值, 还返回该值

```c
int a = 5;
int b;
b = a;
```

​`b = a`​ 这个赋值操作不仅计算了表达式的值(这里是`a`​的值, 即`5`​), 还改变了变量 `b`​ 的状态(`b`​ 从未定义状态变为了 `5`​)

```c
int x, y;
x = (y = 10);  // y被赋值为10, 之后x被赋值为y的值
```

​`y = 10`​ 这个赋值操作改变了变量 `y`​ 的值, 同时返回了 `10`​, 然后 `x = 10`​ 也被执行, 所以, `x`​ 和 `y`​ 最终都变为 `10`​

```c
int a, b, c;
a = b = c = 7;  // 先执行c = 7, 然后b = 7, 最后a = 7
```

‍

‍

C的输入函数

格式化输入函数

字符级输入函数

行级输入函数

二进制输入函数

高级输入技巧

‍

占位符

%g智能显示浮点数：若为整数，显示整数，若为浮点，显示最简形式的浮点数，自动去除末尾的零和无意义的小数点

‍

字符串处理函数

strcspn: `size_t strcspn(const char* str, const char* delim);`​，在str中查找第一个属于字符集合delim的字符，返回索引

c：complement补集，表示函数关注不属于某字符集的部分

spn: span跨度，即计算连续符合某条件的字符长度

必须包含<string.h>, 需要检查空指针和越界访问

常用法：删除换行符

```c
#include <stdio.h>
#include <string.h>

int main() {
    char input[100];
    fgets(input, sizeof(input), stdin); // 读取输入（包含换行符）
    
    size_t pos = strcspn(input, "\n"); // 查找第一个 '\n' 的位置
    if (pos < strlen(input)) {         // 确保找到换行符
        input[pos] = '\0';            // 替换为结束符，删除换行符
    }
    printf("处理后的字符串: %s\n", input);
    return 0;
}
```

‍

strspn: `strspn(str, accept)`​, 计算str开头连续包含在acctpt字符集中的字符数量

‍

C语言常用排序算法

比较函数

需返回整型值：

- **返回值**  **&lt;**  **0**：第一个参数应排在第二个之前。
- **返回值**  **=**  **0**：两参数相等。
- **返回值**  **&gt;**  **0**：第一个参数应排在第二个之后

‍

qsort快速排序：C标准库的快速排序实现

​`void qsort(void* base, size_t nmemb, size_t size, int (*compar)(const void*, const void*));`​

- ​`base`​：待排序数组的首地址。
- ​`nmemb`​：数组元素个数。
- ​`size`​：每个元素的大小（字节数，可用 `sizeof`​ 获取）。
- ​`compar`​：比较函数指针，决定排序顺序

‍

- **时间复杂度**：平均 O(n log n)，最差 O(n²)（取决于数据分布）。
- **空间复杂度**：O(log n)（递归栈）。
- **稳定性**：不稳定。
- **适用场景**：通用排序，适合大规模随机数据

示例

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int arr[] = {5, 2, 8, 3, 1};
    int n = sizeof(arr) / sizeof(arr[0]);

    qsort(arr, n, sizeof(int), compare);

    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]); // 输出：1 2 3 5 8
    }
    return 0;
}
```

‍

‍
