# Python程序运行原理

# **自省机制**

Introspection, Python对象能在运行时动态获取有关自身的信息, 如对象的类型、属性、方法等, Python动态语言特性的核心体现

## **常用自省工具**

- type()和isinstance(): 

  - type(obj): 返回对象的类型
  - isinstance(obj, cls): 检查对象是否是某个类或其子类的实例, 返回布尔值
- dir(): 返回对象的属性和方法列表
- id(): 返回对象的内存地址
- hasattr()、getattr()和setattr()

  - hasattr(obj, attr): 检查对象是否具有指定属性
  - getattr(obj, attr): 获取对象的指定属性值
  - setattr(obj, attr, value): 动态设置对象的属性
- callable(): 检查对象是否可以被调用
- inspect模块: 提供高级的自省功能, 如获取函数签名、源代码等

  ```python
  import inspect
  
  def my_function(a, b):
      return a + b
  
  print(inspect.signature(my_function))  # (a, b)
  print(inspect.getsource(my_function))  # 显示函数源代码
  ```

‍

`sys.modules`: 字典，记录当前Python解释器已经加载的所有模块，键为模块名（字符串），值是模块对象

```python
# 获取当前模块，可使用cur_module.xxx调用当前模块中的函数
cur_module = sys.modules[__name__]
```

‍

## `__all__`

模块级别的特殊变量，**用于声明模块的公开接口**，当使用 `from module import *` 时，只有 `__all__` 列表中指定的对象会被导入

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

`__doc__`文档字符串：Docstring，类、函数定义下被`"""`包裹的注释

```python
print(math.sqrt.__doc__)     # 查看 math.sqrt 的文档
print(pandas.DataFrame.__doc__)  # 查看类的文档
```



## pydoc

命令行工具，在终端生成模块的文档或直接查看

```bash
pydoc math            # 终端查看
pydoc -w math         # 生成 math.html 文档
python -m pydoc math  # 等效于 help(math)
```

## `__file__`

定位模块的源代码路径，适合查看实现细节

```python
import pandas as pd
print(pd.__file__)  # 输出包路径，如 /.../pandas/__init__.py
```

