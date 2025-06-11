# Python面向对象编程

将程序中的数据和行为封装在一起, 以对象的形式表示

> Python中一切皆对象

# 概述

- 类: 对象的模版, 元类的实例

  - 类属性: 直接定义在类中, 被所有对象所共有, 对象、类均可调用, 只能通过类修改

    - `__slot__`​槽: 特殊的类属性, 一种内存和属性管理机制, 内存优化工具, 用于限制类实例可动态添加的属性数量, 修改了类的内部结构(不涉及对象行为的约定, 不是协议)

      - 限制动态属性: 类实例只能拥有slot中指定的属性, 但仍可通过`__dict__`​强制添加(不推荐)

        ```python
        class MyClass:
            __slots__ = ['name', 'age']
      
        obj = MyClass()
        obj.y = 42 # 报错
        obj.__dict__['y'] = 42 # 不报错
        ```
      - 节省内存: Python对象默认有一个`__dict__`​属性用于存储动态添加的属性, `__slot__`​移除了`__dict__`​, 减少了内存开销, 也因此提高了属性的访问速度
      - 注意: `@property`​装饰器装饰的是方法, 因此不打破slot的限制, 实际可用@property装饰一个计算属性(使用已有属性计算得出)

  - 类方法: 不访问对象属性/方法, 只访问类属性的方法, 影响所有实例

    - `@classmethod`​装饰器修饰
    - 定义时第一个参数不是self, 而是<u>cls, 当前类</u>
    - 通过类名(常用, 节约内存)或对象名调用
    - `__new__`​是类方法, 参数为cls
  - 静态方法: 独立功能, 常用作辅助函数, 与类、对象无直接关系, 不访问对象属性/方法或类属性的方法

    - `@staticmethod`​装饰器修饰
    - 通过类名、对象可调用, 仅写在类中, 无self/cls形参
    - 不调用其他方法, 只能调用类的静态成员
  - 类之间的关系: 

    - is-a: 继承/泛化
    - has-a: 关联, 若为整体和部分的关联, 称为**聚合关系, ** 若整体和部分不可分割, 同时存在也同时消亡, 则为最强的关联关系, 称为**合成关系**
    - use-a: 称为**依赖关系**
- 对象: 类的实例

  - 属性: 随着对象创建而创建, 随着对象消失而消失, 多个对象之间相互独立, 互不干扰, 只能对象调用, 类不能调用

    - 实例属性与类属性分离: 给对象添加新属性不会直接影响类属性, 新属性存储在实例的`__dict__`​中, 其他同类实例不受影响
    - 私有属性: 只希望在对象内部被使用, 不希望外部使用, <u>并非强制, 只是提示调用者</u>, 且不影响子类的继承

      - > Python原则: 开放比封闭要好, 'We are all consenting adults here', 我们都是自愿的成年人, 我们应该自己对自己的行为负责而不是从语言层面来限制对数据或方法的访问
        >

      - 在属性/方法前加`__`​

        ```python
        def  __init__(self, name): 
        	# 私有属性
        	self.__age = 18 
        	self.name = name	# print(jerry.__age)会报错, 不能直接访问
        
        # 定义方法访问私有属性
        def secret(self): 
        	print(self.__age) 	# jerry.secret()可以输出18
        
        # 私有方法
        def __secret(self): 
        	print(self.__age) 	# jerry.__secret()会报错, 不能直接调用
        ```
      - 强制访问: `对象._类名__名称`​, 实际上是Python内部对私有属性、私有方法的name添加了前缀, 故无法直接访问

      - 设置访问:

        - 自定义函数方式:

          - 通过自定义getX函数访问私有属性: `get私有属性名`​(首字母大写)
          - 通过自定义setX函数设置私有属性: `set私有属性名`​(首字母大写)
        - 方法+`@property`​装饰器:

          - `@property`​, 属性getter, 只读
          - `@<property_name>.setter`​, 属性setter, 只写, 可添加验证或其他处理逻辑
          - `@<property_name>.deleter`​, 属性deleter
  - 方法: 第一个参数为self, 当前对象(显式传递self, 明确性, 可读性, 灵活性, 避免了隐式行为, 支持多继承)

    - 和函数的区别: 函数不属于任何类, 可直接调用
    - 特殊方法:

      - `__new__()`: 构造方法, 实例化对象, 是`类方法`​，需要返回一个实例，将该实例传递给`__init__`​的self参数
      - `__init__()`: 对象方法，初始化方法, 给对象添加属性并进行赋值, 一参必为self，其他参数与`__new__`​保持一致, 不允许有返回值
      - `__del__()`: 析构方法, 触发时机: 对象无引用(引用计数器为0, 若还有引用过, 则在所有代码执行结束的最后调用)、程序结束、对象被销毁
      - `__call__()`: 对象方法，定义一个对象作为函数调用时的行为, 即用()调用时, 触发该对象的`__call__()`​方法

# 特殊方法

- `id()`: 查看对象的唯一标志(可视为内存地址)
- `dir()`: 用于类名或对象, 内置函数, 返回对象/类的属性和方法列表
- `isinstance(object, classinfo)`: 判断对象是否为某类的实例
- `type()`: 动态创建类

  - 传入一个参数则查看一个变量/对象的类型
  - 传入三个参数则创建一个类，type(name, bases, dict)，name为类名，bases为父类元组，dict为类属性/方法字典

    ```python
    MyClass = type('ClassA', (object,) dict(name="type test"))
    a = MyClass()
  
    # 等同于
    class ClassA:
    	name = "type test"
  
    a = ClassA()
    ```

- `hasattr(object/cls, '属性名')`: 检查对象/类是否有指定名称的属性/方法(包括继承的), 返回布尔值, 反射工具, 用于防御性编程

  - 属性名加引号, 若属性为字符串变量可省略引号
  - 若属性通过`__getattr__`​动态生成, 则`hasattr()`​可能返回False, 除非属性已被实际访问过
- `getattr(object/cls, '属性名', 默认值)`: 动态获取器, 设置默认值可避免报错, 可用于方法

  ```python
  class Dog:
      def bark(self):
          return "汪汪！"
  dog = Dog()
  bark_method = getattr(dog, "bark", None)
  if bark_method:
      print(bark_method())  # 输出: 汪汪！
  ```
- `setattr(object/cls, '属性名', value)`: 动态设置器, 动态地为对象添加或修改属性/方法, 若属性为描述符, 会触发描述符的`__set__`​方法

  ```python
  class Robot:
      pass
  robot = Robot()
  
  # 动态添加方法
  def attack(self):
      return f"{self.name} 发动攻击！"
  
  setattr(Robot, "attack", attack)  # 注意: 这里是给类添加方法
  print(robot.attack())  # 输出: Optimus 发动攻击！
  ```

# 元类

类的模版, 定义类的创建方式、行为和限制, 决定类本身的行为, 类定义中使用metaclass参数, 声明使用哪个元类

type是所有元类的父类, 用来定义和创建类，是一个元类，且为默认的元类

type是object的子类，object是type的一个实例，type的类型是type

![截屏2025-04-07 09.27.25](../assets/截屏2025-04-07%2009.27.25-20250407092728-rokq9io.png)

‍

![截屏2025-01-16 09.20.33](../assets/截屏2025-01-16%2009.20.33-20250116092037-hc8h29l.png)​​

```python
# 定义一个元类
class MyMeta(type):
    # 在类被创建之前, 可以修改类的属性
	def __new__(cls, name, bases, dct):
		dct['attr'] = 'Hello from Meta'
        return super().__new__(cls, name, bases, attrs)

# 通过元类(模具制造机)生成一个类(模具)
Dog = type('Dog', (object,), {'speak': lambda self: 'Woof!'})

# 生成一个对象(饼干)
dog = Dog()
print(dog.speak())  # 输出: 'Woof!'
```

## 定义元类的关键方法

- `__new__(cls, name, bases, dct)`: 创建类, 并控制类的结构, 核心方法

  - cls当前元类, name类名, bases父类元组, 包含所有正在创建类的父类, dct类的属性和方法字典
- `__init__(cls, name, bases, dict)`: 初始化类, new之后额外配置类属性或行为，元类的__init__主要用于类级别的验证/注入逻辑

  - cls：当前正被初始化的类，即元类的实例
  - name：类名
  - bases：类的父类元组
  - dict：类的属性和方法字典
- `__call__(cls, *args, **kwargs)`: 控制如何实例化类, `dog = Dog()`​就是触发了call方法
- `__prepare__(cls, name, bases)`: new之前准备类字典, 定制类定义过程

  ```python
  # 改变类属性的定义顺序
  from collections import OrderedDict
  
  class MyMeta(type):
  	def __prepare__(cls, name, bases):
  		return OrderedDict()
  
  	def __new__(cls, name, bases, dct):
  		print(dct)	# 输出OrderedDict()
  		return super().__new__(cls, name, bases, dct)
  ```

一个对象的创建过程：

![截屏2025-04-07 09.30.47](../assets/截屏2025-04-07%2009.30.47-20250407093049-6uvfs19.png)

## 单例模式

```python
class SingletonMeta(type):
    """
    元类, 用于实现单例模式
    """
	def __init__(cls, *args, **kwargs):
		cls.__instance = None		# 用于存储类的唯一实例
		cls.__lock = threading.RLock()
		super().__init__(*args, **kwargs)

    def __call__(cls, *args, **kwargs):
        """
        控制类的实例化逻辑
        """
        if cls.__instance is None:  # 如果实例不存在, 创建新实例并保存
			with cls.__lock():
				if cls.__instance is None:
					cls.__instance = super().__call__(*args, **kwargs)
        return cls.__instances


class SingletonClass(metaclass=SingletonMeta):
    """
    使用 SingletonMeta 作为元类的类
    """
    def __init__(self, value):
        self.value = value


# 测试单例模式
obj1 = SingletonClass(42)
obj2 = SingletonClass(99)

print(obj1 is obj2)  # 输出 True, 表示两个对象是同一个实例
print(obj1.value)   # 输出 42, 值与第一次实例化时相同
print(obj2.value)   # 输出 42
```

核心思想: 使用元类的`__call__`​方法, 控制类实例的创建, 如果已经存在实例(对象), 则返回现有的实例, 否则创建一个新实例

使用场景: 

- 资源共享: 例如数据库连接池、线程池
- 配置管理: 系统配置、全局配置管理器
- 日志管理: 确保日志对象在整个应用中唯一

# 三大特性

封装性、继承性、多态性

## 继承性

`子类(父类)`​

- 父类/基类/根类&子类/派生类: 多类有相同属性方法, 抽取一类为父类, 子类继承
- 继承特性: 

  - 所有类都有父类, object类是所有类的父类, 无父类时默认父类为object
  - 子类直接继承父类的非私有属性、方法, 不可继承父类的私有属性、方法
  - 父类不能调用子类的方法
- 重写override: 

  - 父子属性不同: 重新初始化

    ```python
    def __init__(self, 公共属性, 新属性):
    	super().__init__(公共属性) 	# 继承公共属性
        self.新属性 = 新属性值
    ```
  - 父子方法不同: 在子类中定义方法, 只有子类可以调用, 若方法名相同, 子类覆盖父类方法
- 多继承: 继承多个父类`class 子类名(父类1,  父类2...):`​, 继承级别从左到右降低, 若父类有相同成员方法或成员变量, 优先继承左父类
- MRO: 方法解析顺序, 只读, 存储C3线性化计算的结果

  - `L[MyClass(Base1, Base2)] = MyClass + merge(L[Base1], L[Base2], Base1, Base2)`​, 即一个类的线性化为本类、父类的线性化、父类列表的合并的总和
  - `类名.mro()`​或`类名.__mro__`​查看
- `super()`​或`super(A, self)`: 查找和调用继承链中的下一个类的方法

  - `super(A, self)`​不是简单的直接访问父类, 而是通过MRO来查找下一个类

    - `A`: 以`A`​为基类, 查找**下一个父类**, 并且调用该父类的`__init__`​方法
    - `self`: 当前实例, `super()`​以此找到正确的上下文
  - 若父类的初始化方法中未使用`super()`​, 会断掉继承链的初始化, 只调用第一个父类的初始化方法, 建议在所有的地方都使用`super()`​

  ```python
  class A:
      def __init__(self):
          print("A's __init__ called")
          super().__init__() # ②调用A的__init__后, 该super仍基于C的MRO, 调用A之后的父类
  
  class B:
      def __init__(self):
          print("B's __init__ called")
          super().__init__() # ③调用后, 调用object的__init__(无操作)
  
  class C(A, B):
      def __init__(self):
          print("C's __init__ called")
          super().__init__() # ①根据C的MRO, 调用A的__init__
  ```
- 抽象类: 不能创建对象, 专门用于被继承, Python没有从语法层面支持抽象类

  - `继承ABC类`​和`@abstractmethod`​装饰器标记抽象方法, 两条件缺一不可

    - @abstractmethod用于标记抽象方法，表示子类必须实现此方法，但仅用`@abstractmethod`​无法定义抽象类, 类仍可实例化
    - 继承ABC使类成为抽象基类，在子类实例化时检查所有抽象方法是否实现
  - 多继承时必须显示调用super(), 确保元类冲突被正确处理
  - ABC和ABCMeta的区别：ABC继承自ABCMeta，内置支持注册虚拟子类，继承ABC即可定义抽象基类，无需显式指定metaclass=ABCMeta, 现代Python推荐继承ABC

  ```python
  from abc import ABCMeta, abstractmethod
  
  class Fighter(object, metaclass=ABCMeta):
  	@abstractmethod
  	def beat():
  		pass
  ```

## 多态性

不同子类调用同一父类方法时, 都调用对象私有方法(即用不同方式实现的父类方法)

支持鸭子类型测试: python不检查是否继承同一父类, 只要方法相同, 就是多态的

# Mixin混入

一种编程范式, 主要用于**类的继承**(将额外功能注入目标类), 允许将多个功能模块化(只实现一个/一组功能), 并将它们'混入'到其他类(常与多继承一起使用)中, 从而实现**代码复用**和**功能扩展**, 不设计为独立的类实例, 不存储状态

```python
# 提供驾驶功能的Mixin类
class DrivableMixin:
    def drive(self):
        print(f'{self.__class__.__name__} is driving.')


# 提供加速功能的Mixin类
class AcceleratableMixin:
    def accelerate(self):
        print(f'{self.__class__.__name__} is accelerating.')


# 目标类, 将Drivable和Acceleratable的功能'混入'到自己身上
class Car(DrivableMixin, AcceleratableMixin):
    def __init__(self, brand):
        self.brand = brand

car = Car('Toyota')
car.drive()         # 输出: Car is driving.
car.accelerate()    # 输出: Car is accelerating.
```

注意:

- 避免状态管理: Mixin不应管理状态或依赖于实例变量, 其目的是提供方法, 而非持有数据
- 避免命名冲突: 多个Mixin可能会提供同名的方法或属性, 设计时需避免命名冲突

  - Python的方法解析顺序(MRO)决定多个类有相同方法时, 哪个方法优先执行
- 避免滥用多继承: Mixin可帮助分割, 但易让类的继承结构变复杂(尤其是多个Mixin嵌套)
- 保持接口一致性: 在不同类中混入多个Mixin时, 应确保其接口一致, 避免不同Mixin之间产生矛盾或依赖关系

```python
# 自定义字典限制只有在指定的key不存在时才能在字典中设置键值对
class SetOnceMappingMixin:
    """自定义混入类"""
    __slots__ = ()

    def __setitem__(self, key, value):
        if key in self:
            raise KeyError(str(key) + ' already set')
        return super().__setitem__(key, value)


class SetOnceDict(SetOnceMappingMixin, dict):
    """自定义字典"""
    pass


my_dict= SetOnceDict()
try:
    my_dict['username'] = 'jackfrued'
    my_dict['username'] = 'hellokitty'
except KeyError:
    pass
print(my_dict)
```

# 面向对象设计原则

- SOLID原则

  - 单一职责原则(SRP): 一个类只负责一个职责或一个功能, 这个原则强调的是**高内聚**、低耦合, 可以降低类的复杂度, 提高代码的可读性、可维护性和可重用性
  - 开闭原则(OCP): 一个类的行为应该是**可扩展**的, 但是**不可修改**, 这个原则强调的是代码的可维护性和可扩展性, 通过抽象化来避免修改已有代码的风险, 从而降低软件维护的成本
  - 里氏替换原则(LSP): 子类应该可以替换其父类并且不会影响程序的正确性, 这个原则强调的是面向对象的继承和多态特性, 通过保证子类的行为和父类一致, 从而提高代码的可维护性和可扩展性
  - 接口隔离原则(ISP): 一个类不应该依赖它不需要的接口, 即一个类对其它类的依赖应该建立在最小的接口上, 这个原则强调的是接口设计的合理性, 避免不必要的接口导致类之间的耦合性过高, 从而提高代码的灵活性和可维护性
  - 依赖倒置原则(DIP): **依赖于抽象**而不是依赖于具体实现, 这个原则强调的是代码的可扩展性和可维护性, 通过抽象化来减少组件之间的耦合性, 从而使得代码更加灵活、易于维护和扩展
  - SOLID原则的扩展: 

    - KISS原则: Keep It Simple, Stupid, 系统设计应该保持简单, 避免不必要的复杂性
    - YAGNI原则: You Aren't Gonna Need It, 不要为将来可能需要的功能过度设计或预先实现, 专注于当前需求, 避免浪费时间和资源

- 迪米特法则(LoD): 也叫最少知识原则(LKP), 一个对象应当对其他对象有尽可能少的了解, 不需要了解的内容尽量不要去了解, 这个原则强调的是组件之间的松耦合, 通过减少组件之间的依赖关系, 提高代码的可维护性和可重用性
- 组合/聚合复用原则(CARP): 尽量使用组合或聚合关系, 而不是继承关系来达到代码复用的目的, 这个原则强调的是通过组合和聚合的方式来实现代码复用, 避免继承带来的一些问题, 如父类和子类之间的强耦合性, 从而提高代码的灵活性和可维护性

# GoF设计模式

设计模式: 指一套被广泛接受的、可重复使用的软件设计解决方案

23种, 三类

## 创建型模式

涉及对象的创建机制, 并提供了一种**将对象的创建和使用分离**的方式

- 工厂模式: 定义一个创建对象的接口, 让子类决定实例化哪一个类, 工厂方法使一个类的实例化延迟到其子类
- 抽象工厂模式: 提供一个创建一系列相关或相互依赖对象的接口, 而无需指定它们的具体类
- 单例模式: 确保一个类只有一个实例, 并提供对该实例的全局访问点
- 建造者模式: 将一个复杂对象的构建与它的表示分离, 使得同样的构建过程可以创建不同的表示
- 原型模式: 通过复制现有的实例来创建新的对象, 而不是使用构造函数

## 结构型模式

涉及将类或对象组合在一起形成更大的结构, 并提供了一种简化设计的方式

- 适配器模式: 将一个类的接口转换成客户希望的另一个接口, 适配器模式可以让原本由于接口不兼容而不能在一起工作的类可以一起工作
- 桥接模式: 将抽象部分与它的实现部分分离, 使它们都可以独立地变化
- 组合模式: 将对象组合成树形结构以表示部分-整体的层次结构 组合模式使得用户对单个对象和组合对象的使用具有一致性
- 装饰器模式: 动态地将责任附加到对象上, 提供了一种灵活的替代继承的方式
- 外观模式/门面模式: 为子系统中的一组接口提供一个一致的界面, 使得子系统更容易使用
- 享元模式: 运用共享技术来有效地支持大量细粒度对象的复用
- 代理模式: 为其他对象提供一种代理以控制对这个对象的访问, 代理对象可以在被代理对象执行操作前后进行一些预处理和后处理

## 行为型模式

涉及对象之间的通信和算法的分配, 并提供了一种实现松散耦合的方式

- 责任链模式: 为解除请求的发送者和接收者之间耦合, 而使多个对象都有机会处理这个请求
- 命令模式: 将请求封装成一个对象, 从而使你可以用不同的请求对客户进行参数化, 命令模式也支持撤销操作
- 解释器模式: 是一种行为型设计模式, 它提供了一种方法, 可以在运行时解释语言文法中的表达式, 并执行相应的操作
- 迭代器模式: 提供一种方法顺序访问一个聚合对象中的各个元素, 而又不暴露该对象的内部表示
- 中介者模式: 用一个中介对象来封装一系列的对象交互, 中介者使各个对象不需要显式地相互作用, 从而使其耦合松散, 而且可以独立地改变它们之间的交互
- 备忘录模式: 在不破坏封装性的前提下, 捕获一个对象的内部状态, 并在该对象之外保存这个状态, 备忘录模式可以在需要时将对象恢复到先前的状态
- 观察者模式: 定义对象间的一种一对多的依赖关系, 使得每当一个对象状态发生改变时, 所有依赖它的对象都会得到通知并自动更新
- 状态模式: 允许对象在其内部状态发生改变时改变它的行为, 对象看起来似乎修改了它的类
- 策略模式: 定义一系列算法, 将每个算法都封装起来, 并使它们之间可以互换, 使得算法可以独立于使用它的客户而变化

  ```python
  # 可插拔的哈希算法
  class StreamHasher:
      """哈希摘要生成器"""
  
      def __init__(self, alg='md5', size=4096):
          self.size = size
          alg = alg.lower()
          self.hasher = getattr(__import__('hashlib'), alg.lower())()
  
      def __call__(self, stream):
          return self.to_digest(stream)
  
      def to_digest(self, stream):
          """生成十六进制形式的摘要"""
          for buf in iter(lambda: stream.read(self.size), b''):
              self.hasher.update(buf)
          return self.hasher.hexdigest()
  
  def main():
      """主函数"""
      hasher1 = StreamHasher()
      with open('Python-3.7.6.tgz', 'rb') as stream:
          print(hasher1.to_digest(stream))
      hasher2 = StreamHasher('sha1')
      with open('Python-3.7.6.tgz', 'rb') as stream:
          print(hasher2(stream))


  if __name__ == '__main__':
      main()
  ```
- 模板方法模式: 定义一个算法框架, 并将一些步骤延迟到子类中实现, 以便在不改变算法结构的情况下, 允许子类重定义算法的某些步骤
- 访问者模式: 是一种行为型设计模式, 它可以让你在不修改对象结构的前提下, 定义作用于这些对象元素的新操作

‍
