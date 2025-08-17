# Flask

# 概述

- 特点: 核心代码不到5000, 轻量灵活, 简单易用, 功能可扩展
- 微框架设计理念, 只提供最核心的功能: 

  - 路由系统: 使用装饰器模式定义路由, 支持<u>动态路由</u>参数, 提供了强大的URL转换器, 可自动解析URL参数类型, 底层依赖Werkzeug作为WSGI工具包(Python Web框架和Web服务器之间的接口标准)

    ```python
    @app.route('/user/<int:id>')
    def get_user(id):
        return f'User ID: {id}'
    ```
  - 请求和响应对象
  - 模版渲染
  - 上下文管理机制: 提供两个核心的上下文对象, 可通过全局变量(request、g)来访问请求数据, 不用显式传递参数, 这些全局变量实际上为线程本地存储, 避免多线程时数据混乱

    - request context: 每个请求都有自己的上下文, 包含请求数据、url参数、header等
    - application context: 包含应用程序的全局对象, 如配置和数据库连接

    ```python
    @app.route('/hello')
    def hello():
        user_agent = request.headers.get('User-Agent')
        return f'Your browser is {user_agent}'
    ```
- 扩展库: 

  - Flask-SQLAlchemy: 数据库ORM
  - Flask-Migrate: 数据库迁移工具
  - Flask-WTF: 表单验证
  - Flask-Login: 用户认证

‍

# 项目配置

- debug mode: 热更新, 在编辑配置中更改

- 修改host: 让其他电脑能访问本级flask项目, 编辑配置`--host=0.0.0.0`​

- 修改port: 编辑配置`--port=8000`​

![截屏2025-01-06 18.14.06](../../assets//截屏2025-01-06%2018.14.06-20250106181409-7jupsbg.png)​​![截屏2025-01-06 18.14.27](../../assets//截屏2025-01-06%2018.14.27-20250106181429-has8m0e.png)![截屏2025-01-06 18.14.51](../../assets//截屏2025-01-06%2018.14.51-20250106181455-bs26jnz.png)

或者在`app.run()`​中加参数: 

```python
app.run(debug=True, host='0.0.0.0', port=8080)
```

# URL与视图映射

## 定义无参url

```python
@app.route('/')
def hello_world():
	pass

@app.route('/profile')
def profile():
	return '我是个人中心！'
```

## 定义带参url

用尖括号包括参数名, 可加类型在前

flask接到请求后解析url中参数, 传递给视图函数

```python
@app.route('/blog/<int:blog_id>')
def blog_detail(blog_id):
	return '您访问的博客是: %s' % blog_id
```

参数类型可为string、int、float、path、uuid、any

any: 被选值中的任意一个

```python
@app.route('/blog/list/<any(python, flask, django): category>')
def blog_list_with_category(category):
	return '您获取的博客分类为: %s' % category
```

查询字符串的方式传参: 

/blog/list: 返回第一页

/blog/list?page=2: 获取第二页的数据

```python
@app.route('/blog/list')
def blog_list():
    page = request.args.get('page', default=1, type=int)
    return f'您获取的是第{page}页的博客列表!'
```

# Jinja2

Flask渲染HTML使用的默认模版引擎, 也可用于Django, 高效, 可扩展

避免在后端代码中出现大片html富文本

## 模版渲染

```python
# 导入render_template
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def hello_workd():
	return render_template('index.html') 	# 使用render函数
```

路径必须在template文件夹下: 

![截屏2025-01-06 20.36.56](../../assets//截屏2025-01-06%2020.36.56-20250106203658-7vou5u9.png)

可传递其他参数: 

```python
@app.route('/blog/<blog_id>')
def blog_detail(blog_id):
    return render_template('blog_detail.html',
                           blog_id=blog_id,
                           username='Myself')
```

blog_detail.html: 

```html
<body>
    <p>您的用户名为: {{ username }}</p>
    <h1>您访问的博客详情是: {{ blog_id }}</h1>
</body>
```

## 模版访问对象属性、字典值

可通过类对象或字典的`.`​运算符访问值

```python
class User:
    def __init__(self, username, email):
        self.username = username
        self.email = email

@app.route('/')
def hello_world():  # put application's code here
    user = User(username="Myself", email="xxxx@qq.com")
    return render_template('index.html', user=user)
```

index.html: 

```html
<body>
    {{ user.username}} / {{ user.email }}
</body>
```

## 过滤器

本质为python函数, 把被过滤的值当作第一参数传给该函数, 函数处理后返回新值

调用方法: `|`​管道符, 如{name｜length}, 把name作为一参传给length过滤器底层的函数

```python
@app.route('/filter')
def filter_demo():
    user = User(username="Myself", email="xxxx@qq.com")
    return render_template('filter.html', user = user)
```

filter.html

```html
<body>
{{ user.username }} -- {{ user.username|length }}
</body>
```

### 内置过滤器

更多详见官方文档

abs(value): 取value的绝对值

default(value, default_value, boolean=False): 若value无定义, 则返回二参default_value, 三参为是否开启value为空时使用默认值

```html
<div>default过滤器: {{ user|default('admin') }}</div>
```

escape(value): 将特殊字符转义, Jinja2默认全局转义, 只有关闭转义时可能用到escape过滤器

filesizeformat(value, binary=False): 将文件大小格式化为方便阅读的单位, 如kB, MB, Bytes等, 二参设置相邻单位换算为1024倍

first(value): 返回value序列的第一个元素

float(value, default=0.0): 将value转float类型, 失败则返回0.0

format(value, *args, **kwargs): 格式化字符串

```html
{{ "%s,%s"|format(greeting, name) }}
```

groupby(value, attribute, default=None): value为序列, 可用参数attribute进行分组, 如user列表, 用user的city属性

```html
<!-- {% %} 为开头和结尾符 -->
{% for group in users| groupby('city') %}  
```

‍

定义过滤器的方法: @app.template_filter装饰器或app.add_template_filter函数

```python
# add_template_filter()

def date_format(value, format='%Y-%d-%m %H:%M'):
	return value.strftime(format)

# 将date_format函数注册成过滤器, 且过滤器名为dformat
# 若未传二参, 默认使用函数的名称作为过滤器的名称
app.add_template_filter(date_format, 'dformat')
```

之后可在模版文件中: 

```html
{{ article.pub_date|dformat }}
{{ article.pub_date|dformat('%B %Y') }}
```



# 布隆过滤器

布隆过滤器(**Bloom Filter**)是一种概率型数据结构, 用于测试一个元素是否在一个集合中。它具有非常高的空间效率, 尤其适用于大数据集的快速查询, 但存在一定的误判率, 即可能会错误地报告某个元素存在于集合中(假阳性)。不过, 它**不会漏掉任何真实存在的元素**(没有假阴性)。

### 特点: 

- **空间效率高**: 布隆过滤器占用的内存空间非常小。
- **查询速度快**: 查询操作是常数时间复杂度 O(k), 其中 k 是哈希函数的数量。
- **误判率**: 存在误判的可能性, 即某个元素可能被错误地判定为在集合中, 但实际上并不在；但是不会发生漏判, 即对于在集合中的元素, 一定能正确判断。

### 工作原理: 

布隆过滤器的核心思想是使用多个哈希函数将元素映射到一个位数组(bit array)中, 每个哈希函数将元素映射为数组中的一个位置, 将该位置标记为1。查询时, 使用相同的哈希函数对查询的元素进行映射, 如果所有映射位置的值都为1, 则表示该元素可能存在；否则, 元素一定不在集合中。

#### 1. **添加元素**: 

- 对于要添加的元素, 使用多个哈希函数计算出多个位置, 并将这些位置的位设置为 1。

#### 2. **查询元素**: 

- 对于查询的元素, 使用相同的哈希函数计算多个位置。如果所有计算出的位都为 1, 则表示该元素可能存在；如果有任意一个位为 0, 则表示该元素一定不在集合中。

### 关键参数: 

- **位数组大小(m)** : 决定布隆过滤器的存储大小, 位数组越大, 误判率越低。
- **哈希函数数量(k)** : 哈希函数的数量, 更多的哈希函数可以降低误判率, 但会增加计算时间。

### 假阳性与假阴性: 

- **假阳性**: 查询时, 布隆过滤器可能会错误地报告某个元素存在于集合中, 但实际上它并不在集合中。布隆过滤器的误判率与位数组大小和哈希函数数量密切相关。
- **假阴性**: 布隆过滤器不会发生假阴性。即, 如果一个元素确实在集合中, 布隆过滤器一定会正确地报告它存在。

### 典型应用: 

1. **Web 搜索引擎**: 用于过滤已访问过的网页, 避免重复计算。
2. **分布式系统**: 在大规模分布式系统中, 用于快速判断某个元素是否在分布式缓存或存储中。
3. **数据库**: 例如, HBase 和 Redis 使用布隆过滤器来加速查询操作。
4. **网络路由**: 如防火墙中的 IP 黑名单过滤等。

### 示例代码(Python 实现): 

```python
import hashlib

class BloomFilter:
    def __init__(self, size, hash_count):
        self.size = size  # 位数组大小
        self.hash_count = hash_count  # 哈希函数数量
        self.bit_array = [0] * self.size  # 初始化位数组
    
    def _hash(self, item, seed):
        """简单的哈希函数, 基于字符串和种子"""
        return hashlib.md5((str(seed) + item).encode('utf-8')).hexdigest()
    
    def add(self, item):
        """将元素添加到布隆过滤器"""
        for i in range(self.hash_count):
            hash_value = self._hash(item, i)
            index = int(hash_value, 16) % self.size
            self.bit_array[index] = 1
    
    def contains(self, item):
        """检查元素是否存在于布隆过滤器中"""
        for i in range(self.hash_count):
            hash_value = self._hash(item, i)
            index = int(hash_value, 16) % self.size
            if self.bit_array[index] == 0:
                return False  # 如果有一个位置是 0, 说明一定不在集合中
        return True  # 如果所有位置都是 1, 说明可能在集合中

# 使用示例
bf = BloomFilter(1000, 3)  # 位数组大小1000, 3个哈希函数
bf.add("apple")
bf.add("banana")

print(bf.contains("apple"))  # 输出: True
print(bf.contains("banana"))  # 输出: True
print(bf.contains("cherry"))  # 输出: False
```

### 总结: 

布隆过滤器是一种高效的空间节省数据结构, 适用于大规模数据的快速查询场景, 尤其是在内存受限或需要高查询速度的情况下。尽管它存在误判的风险, 但在很多应用中, 容忍一定的误判率是可以接受的, 尤其是在需要快速判断某元素是否在集合中的情况下。

## 解决缓存穿透问题

布隆过滤器可以有效地解决 **缓存穿透**(Cache Penetration)问题, 以下是详细的解释和解决方案。

### 什么是缓存穿透？

缓存穿透是指请求的数据在缓存中不存在, 且该数据本身也不在数据库中。当缓存层和数据库层都无法提供所请求的数据时, 导致请求直接穿透缓存层, 访问数据库。这会导致大量无效查询直接打到数据库上, 增加数据库的压力, 降低系统的性能。

### 如何使用布隆过滤器解决缓存穿透问题？

布隆过滤器可以帮助防止无效的请求直接访问数据库, 从而减少缓存穿透的发生。具体的解决方案如下: 

### 1. **布隆过滤器的工作原理**

布隆过滤器通过 **哈希函数** 和 **位数组** 来标记集合中是否包含某个元素。它的特点是: 

- **快速查询**: 布隆过滤器能够快速判断某个元素是否存在于集合中。
- **误判**: 布隆过滤器可能会错误地返回"存在"(假阳性), 但是它不会漏判(假阴性)。

### 2. **结合布隆过滤器与缓存解决缓存穿透**

在解决缓存穿透时, 可以通过在缓存和数据库之间使用布隆过滤器来阻止无效数据的查询。

#### 具体步骤: 

1. **在布隆过滤器中存储有效的查询键**: 

    - 在将数据存储到缓存之前, 首先通过布隆过滤器检查该查询的 **键** 是否有效。如果布隆过滤器标记该键为"存在", 则查询可以继续进行；如果标记为"不存在", 则说明该数据在数据库中也不存在, 可以跳过查询数据库的操作。
2. **当请求数据时**: 

    - 在缓存系统中, 首先使用布隆过滤器判断请求的键是否可能存在。如果布隆过滤器返回该键可能存在, 就去缓存中查询。
    - 如果缓存中没有数据, 再查询数据库, 并且将结果放入缓存。
    - 如果布隆过滤器返回该键不存在, 直接返回空值或者默认值, 而不去访问数据库, 避免无效查询。

#### 伪代码示例: 

```python
class CacheWithBloomFilter:
    def __init__(self, bloom_filter, cache, db):
        self.bloom_filter = bloom_filter  # 布隆过滤器
        self.cache = cache  # 缓存
        self.db = db  # 数据库

    def get_data(self, key):
        # 1. 使用布隆过滤器检查该 key 是否存在
        if not self.bloom_filter.contains(key):
            # 如果布隆过滤器表明数据不在数据库中, 直接返回空
            return None
        
        # 2. 查询缓存
        data = self.cache.get(key)
        if data:
            return data  # 如果缓存中有数据, 直接返回
        
        # 3. 缓存中没有数据, 查询数据库
        data = self.db.get(key)
        
        if data:
            # 4. 将查询结果放入缓存
            self.cache.set(key, data)
        else:
            # 如果数据库中没有数据, 可以选择将该 key 加入布隆过滤器, 
            # 防止以后再查询时直接去数据库
            self.bloom_filter.add(key)
        
        return data
```

### 3. **具体细节和优势**

- **减少数据库查询**: 当请求的数据不在数据库中时, 布隆过滤器能够直接告诉我们, 这样我们就不会继续查询数据库, 避免了无效的数据库请求。
- **降低数据库压力**: 布隆过滤器能够有效过滤掉大量无效请求, 减少数据库的负担, 提升系统的整体性能。
- **处理错误的数据访问**: 布隆过滤器能够防止无效的缓存查询穿透到数据库, 避免了缓存层失效时直接打到数据库上造成的性能问题。

### 4. **布隆过滤器的误判问题**

由于布隆过滤器可能会发生**假阳性**(即报告某个元素可能存在, 但实际上它并不在集合中), 但它 **不会漏判**(即, 如果一个元素确实不在集合中, 它会报告为不存在)。这意味着, 对于某些"误判"的情况, 虽然布隆过滤器可能会告诉我们一个元素可能在数据库中, 但实际上它不存在, 我们仍然会查询数据库并得到 `None`​ 或其他错误数据。为了避免误判带来的影响, 布隆过滤器的 **误判率** 可以通过合理配置位数组的大小和哈希函数数量来控制。

### 5. **优缺点总结**

#### 优点: 

- **高效**: 布隆过滤器非常节省空间, 可以在大数据量的情况下有效减少数据库的查询。
- **减少不必要的数据库访问**: 避免无效的请求直接打到数据库, 减轻数据库压力。

#### 缺点: 

- **可能的假阳性**: 布隆过滤器可能会误认为某个元素存在, 但实际上它并不在集合中。尽管如此, 这不会影响数据库的正确性, 因为即使发生假阳性, 数据库依然能返回正确的结果。

### 总结: 

布隆过滤器通过有效过滤掉无效的请求, 能够解决缓存穿透问题。它可以确保缓存系统只查询那些可能存在于数据库中的数据, 从而减少不必要的数据库访问, 提高系统性能。在使用时, 需要平衡布隆过滤器的误判率和内存占用, 通过调整位数组大小和哈希函数数量来优化性能。

‍





## 控制语句

### if

有if就有endif, 语句均为{%开头, %}结尾

```html
{% if age > 18 %}
    <div>您已成年, 可以进屋</div>
{% elif age > 35 %}
    <div>您已超龄, 注意身体</div>
{% else %}
    <div>您还小呢, 别急</div>
{% endif %}
```

### for

有for就有endfor, 语句均为{%开头, %}结尾

```html
{% for book in books %}
<div>图书名称: {{ book.name }}  图书作者: {{ book.author }}</div>
{% endfor %}
```

## 模版继承

大部分网页的模块是重复的

extends关键字, 声明继承

child1.html

```html
{% extends 'base.html' %}
```

用子模版中定义的block替换父模版中的block

child1.html:

```html
{% extends 'base.html' %}

{% block title %}
	我是child1的title
{% endblock %}

{% block body %}
    我是child1的body
{% endblock %}
```

base.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}{% endblock %}</title>  <!-- 用子模块中的block替换-->
</head>
<body>
<ul>
    <li><a href="#">首页</a></li>
    <li><a href="#">新闻</a></li>
</ul>

{% block body %}{% endblock %}
</body>
<footer>这是底部的标签</footer>
</html>
```

## 加载静态文件

静态文件, 一般为css、js、图片等文件, 默认存放在项目的static文件夹中

可自定义静态文件目录: 

```python
app = Flask(__name__, static_folder='path')
```

在模版文件中, 可通过url_for加载静态文件

```html
<link href="{{ url_for('static',filename='about.css') }}">
<!-- 被解析成: <link href="/static/about.css"> -->
```

一参static固定, 二参filename为文件名/路径, 相对于static或自定义的路径

```python
@app.route('/static')
def static_demo():
    return render_template('static.html')
```

static.html

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
	<!-- css文件 -->
    <link rel="stylesheet" href="{{ url_for('static', filename='/css/style.css') }}">
	<!-- js文件 -->
    <script src="{{ url_for('static', filename='/js/my.js') }}"></script>
</head>
<body>
	<!-- 图片文件 -->
    <img src="{{ url_for('static', filename='/images/pic1.jpg') }}" alt="">
</body>
```

```
# style.css
body{
    background-color: greenyellow;
}

# my.js
alert('我是my.js中执行的！')
```

![截屏2025-01-07 08.58.24](../../assets//截屏2025-01-07%2008.58.24-20250107085827-xs5qnn6.png)

# Flask-SQLALchemy

## ORM

Object-Relational Mapping, 对象关系映射, 一种<u>用代码代替SQL</u>的编程方式, 将面向对象的编程语言与关系型数据库之间的转换自动化, 即表对应类, 行对应对象, 列对应对象属性, 实现不用写sql查询, 通过操作对象与数据库交互, 隐藏了数据库的底层操作, 适用于大多数Web项目, 提升开发效率

### **核心功能**

1. 自动映射: 数据库的表和列自动映射为类和属性
2. 数据操作: 增删改查操作通过调用类的方法来实现, 而不是写SQL语句
3. 关系管理: ORM 可以自动处理一对多、多对多等表关系

### **常见Python ORM框架**

|**框架**|**描述**|
| --| ----------------------------------|
|**SQLAlchemy**|最流行的 Python ORM 库, 功能强大|
|**Django ORM**|Django 框架内置的 ORM|
|**Peewee**|轻量级 ORM, 适合小型项目|
|**Tortoise-ORM**|支持异步操作的 ORM|

### **优缺点**

**优点: **

1. 提高开发效率: 用类和对象操作数据库, 不需要手写SQL
2. 减少代码冗余: 自动完成对象和表之间的转换
3. 更安全: 避免了 SQL 注入等安全问题
4. 更易维护: 业务逻辑与数据操作逻辑解耦, 代码可读性高

**缺点: **

1. 性能开销: ORM 的抽象层可能会导致性能问题, 尤其是处理大批量数据时
2. 学习曲线: 掌握 ORM 的映射规则和用法需要时间
3. 复杂查询难以表达: 一些复杂的 SQL 查询(如多表 JOIN、窗口函数)在 ORM 中难以实现, 可能需要回退到原生 SQL

### 示例

传统 SQL 写法: 

```python
import sqlite3

# 连接数据库
conn = sqlite3.connect('example.db')
cursor = conn.cursor()

# 创建表
cursor.execute('''
CREATE TABLE Users (
    id INTEGER PRIMARY KEY,
    name TEXT,
    email TEXT
)
''')

# 插入数据
cursor.execute('''
INSERT INTO Users (name, email)
VALUES ('Alice', 'alice@example.com')
''')

# 查询数据
cursor.execute('SELECT * FROM Users')
users = cursor.fetchall()
print(users)

# 关闭连接
conn.close()
```

ORM 写法(Flask-SQLAlchemy): 

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///example.db'
db = SQLAlchemy(app)

# 定义模型类
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(80), nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)

# 创建数据库
with app.app_context():
    db.create_all()

# 插入数据
with app.app_context():
    user = User(name='Alice', email='alice@example.com')
    db.session.add(user)
    db.session.commit()

# 查询数据
with app.app_context():
    users = User.query.all()
    for user in users:
        print(user.name, user.email)
```

## Flask-SQLAlchemy的使用

### 连接mysql

```python
from flask import Flask
# 导包
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy import text

app = Flask(__name__)

# MySQL所在主机名
HOSTNAME = '127.0.0.1'
#MySQL监听的端口号, 默认3306
PORT = 3306
# 连接MySQL的用户名和密码
USERNAME = 'root'
PASSWORD = '12345678'
# MySQL上创建的数据库名称
DATABASE = 'database_learn'

# 绑定配置信息
app.config['SQLALCHEMY_DATABASE_URI'] = f'mysql+pymysql://{USERNAME}:{PASSWORD}@{HOSTNAME}:{PORT}/{DATABASE}?charset=utf8mb4'

# SQLAlchemy会自动读取app配置信息中连接数据库的信息
db = SQLAlchemy(app)

# 测试连接
with app.app_context():  	# 上下文管理器
    with db.engine.connect() as conn:
        rs = conn.execute(text('select 1'))
        print(rs.fetchone())
```

### 创建ORM模型

```python
# 必须继承db.Model类
class User(db.Model):
    __tablename__ = 'user'
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    username = db.Column(db.String(80), nullable=False)
    password = db.Column(db.String(80), nullable=False)


# 激活flask的应用上下文, 让SQLAlchemy知道在哪个应用环境中操作
with app.app_context(): 
	# 创建所有表
    db.create_all()
```

`db.create_all()`: 扫描所有继承自`db.Model`​的类, 识别模型定义,  检查数据库中是否已经存在表, 如果表不存在, 就创建表, 只会创建不存在的表, <u>不会修改已有表的结构</u>, 修改表结构需要用迁移工具, 如`Flask-Migrate`​来更新表结构

`db.drop_all()`: 删除所有表, 用于清空数据库(开发环境常用)

### CRUD操作

add操作

```python
@app.route('/user/add')
def add_user():
    # 创建ORM对象
    user = User(username='法外狂徒', password='54321')
    # 将ORM对象添加到db.session中
    db.session.add(user)
    # 将db.session中的改变同步到数据库中
    db.session.commit()
    return '用户创建成功!'
```

query操作

```python
@app.route('/user/query')
def query_user():
    # 1. get查找: 根据主键查找
    # user = User.query.get(1)
    # print(f'{user.username} -- {user.password}')

    # 2. filter_by查找
    users = User.query.filter_by(username='法外狂徒')
    for user in users:
        print(user.username)

    return '数据查找成功!'
```

update操作: 

```python
@app.route('/user/update')
def update_user():
	# filter_by返回类数组类型, 所以要加.first()
    user = User.query.filter_by(username='法外狂徒').first()
    user.password = '123456'
    db.session.commit()
    return '数据修改成功!'
```

delete操作: 

```python
@app.route('/user/delete')
def delete_user():
    # 1. 查找
    user = User.query.get(1)
    # 2. 从db.session中删除
    db.session.delete(user)
    # 3. 将db.session中的修改同步到数据库中
    db.session.commit()
    return '数据删除成功!'
```

### 外键

#### 设置外键关系需两部分: 

1. 外键字段的声明: 通过`ForeignKey`​设置约束, 数据库级别的约束, 用于在数据库表之间建立关系, class中`db.Column(db.ForeignKey())`​, 单向引用
2. 模型之间的关系声明: 通过`relationship`​建立面向对象的关联、ORM层面的模型关系\, <u>不是数据库的外键约束</u>, 而是为了方便在Python代码中操作关联对象, 可自动处理懒加载和级联删除等高级功能, 双向引用

#### back_populates和bcackref:

- back_populates: 需要在两边都写, 但更灵活, 提供了更多的配置选项, 适合复杂关系, 当关系需要额外配置时更推荐
- backref: 只需在一边写, 简单, 关系简单时使用, 减少重复代码, 可理解为back_populates的简化语法糖, <u>相当于给父表增加了一个外表的属性</u>, 一般写backref够用, 且最好<u>总是在父表中声明backref</u>

```python
class User(db.Model):
    __tablename__ = 'user'
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    username = db.Column(db.String(80), nullable=False)
    password = db.Column(db.String(80), nullable=False)
	# 声明模型关系
	articles = db.relationship('Article', backref='author')


class Article(db.Model):
    __tablename__ = 'article'
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    title = db.Column(db.String(80), nullable=False)
    content = db.Column(db.Text, nullable=False)
    # 添加外键
    author_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)


article = Article(title='Flask学习大纲', content='Flaskxxx')
print(article.author)


# 建立外键后可通过外键查找被引用的父表的数据
@app.route('/article/add')
def add_article():
    article1 = Article(title='first article', content='first content')
    article2 = Article(title='second article', content='second content')
    article1.author = User.query.get(2)
    article2.author = User.query.get(2)
    db.session.add_all([article1, article2])
    db.session.commit()
    return '文章添加成功!'


@app.route('/article/query')
def query_article():
    user = User.query.get(2)		# article引用user表, 但通过user可以看article
    for article in user.articles:
        print(article.title)
    return '文章查找成功!'
```

‍

### migrate迁移ORM模型

```python
from flask_migrate import Migrate
...
db = SQLAlchemy(app)
migrate = Migrate(app, db)
```

ORM模型映射成表三部曲, 终端cd到项目目录后: 

1. `flask db init`​, 只运行一次, 结束后会在项目目录生成migrate文件夹, 后续多次改变只运行后两步即可
2. `flask db migrate`​, 识别ORM的改变, 生成迁移脚本
3. `flask db upgrade`​, 运行迁移脚本, 同步到数据库中

会生成alembic_version表, 记录当前版本



# 映射器

### 1. **​`class SubjectMapper(ModelMapper):`​** ​

这行代码定义了一个名为 `SubjectMapper`​ 的类, 它继承自 `ModelMapper`​ 类。这个类通常用于将 `Subject`​ 模型(或数据结构)映射到其他形式的数据表示, 或者进行某种转换操作。

#### **​`ModelMapper`​**​ **的含义**: 

- **​`ModelMapper`​**​ 是一种常见的设计模式, 通常用于数据映射。这个类可能是你项目中的一个基础类, 或者来自某个库, 通常用于处理对象之间的转换(例如, ORM(对象关系映射)库中的数据模型与视图、DTO(数据传输对象)之间的映射)。
- 在某些框架中, `ModelMapper`​ 可能用于将数据库模型(例如 `Subject`​)映射到其他对象或数据结构, 如表单对象、API 响应对象等。

#### **​`class Meta:`​** ​ **和** **​`model = Subject`​**: 

在这个类中, `Meta`​ 是一个内部类, 它通常用来定义一些与模型相关的配置。`Meta`​ 类的内容取决于你使用的框架或库。在这个上下文中, `model = Subject`​ 表明 `SubjectMapper`​ 类与 `Subject`​ 模型进行映射。

- **​`model = Subject`​**: 表示 `SubjectMapper`​ 类是专门为 `Subject`​ 模型设计的映射器。这意味着该映射器会定义如何从 `Subject`​ 模型实例中提取数据或将数据填充到 `Subject`​ 模型中。

### 2. **映射器(Mapper)解释**

**映射器(Mapper)**  是一个用于转换数据结构的工具或设计模式。它的目的是将一种类型的数据转换为另一种类型, 通常用于以下场景: 

#### **常见用途: **

1. **数据传输对象(DTO)和实体之间的映射**: 

    - 在很多应用中, 数据通常从数据库模型(如 ORM 模型)传递到前端应用或外部 API, 这时需要使用映射器来转换数据。举例来说, `SubjectMapper`​ 可以将 `Subject`​ 模型转换为一个适合于前端展示的对象, 或者将用户输入的数据填充到模型对象中。
2. **对象转换**: 

    - 映射器也可以用于将一个对象的属性映射到另一个对象的属性上。例如, 从一个数据库查询结果(通常是字典或其他数据结构)转换为模型对象实例, 或者反过来将一个模型对象转换为字典、JSON 等格式。
3. **领域模型和视图模型之间的映射**: 

    - 在 MVC(Model-View-Controller)或 MVVM(Model-View-ViewModel)架构中, 映射器经常用于将领域模型与视图模型之间的数据转换。

#### **映射器的常见类型: **

1. **实体到视图模型的映射**: 

    - 例如, 将数据库中的 `Subject`​ 对象映射到一个适用于用户界面的视图模型(比如展示用的字典或 JSON 对象)。
2. **DTO 到实体的映射**: 

    - 将用户提交的表单数据(通常是 JSON 或类似格式的 DTO)映射到实际的数据库模型对象(如 `Subject`​)。

#### **示例: **

假设我们有一个 `Subject`​ 模型, 表示学校的课程信息: 

```python
class Subject:
    def __init__(self, id, name, description):
        self.id = id
        self.name = name
        self.description = description
```

我们需要将 `Subject`​ 模型的数据转换成前端使用的字典对象: 

```python
class SubjectMapper(ModelMapper):
    class Meta:
        model = Subject
    
    def to_dict(self, subject_instance):
        return {
            'id': subject_instance.id,
            'name': subject_instance.name,
            'description': subject_instance.description
        }

    def from_dict(self, data):
        return Subject(
            id=data['id'],
            name=data['name'],
            description=data['description']
        )
```

在这个示例中: 

- `to_dict()`​ 方法将 `Subject`​ 实例转换为字典格式, 方便前端展示。
- `from_dict()`​ 方法将字典格式的数据转换回 `Subject`​ 实例, 通常用于处理来自前端的请求数据。

### 总结: 

- **​`SubjectMapper`​**​ 是一个继承自 `ModelMapper`​ 的类, 用于将 `Subject`​ 模型的数据进行映射。它帮助在不同的数据结构(如数据库模型、DTO、前端展示对象)之间进行转换。
- **映射器(Mapper)**  是一个用于在不同类型的数据之间进行转换的设计模式, 它简化了不同数据结构之间的转换操作, 常用于数据库模型与视图模型、DTO 之间的转换。

‍

---

‍

# DRF的序列化器

这两个类 `SubjectSerializer`​ 和 `SubjectMapper`​ 看似做了类似的事情, 但它们的用途和功能在细节上是不同的。以下是它们的主要区别和作用: 

### 1. **​`SubjectSerializer`​**​ **类**(使用 Django REST framework 的序列化器)

```python
class SubjectSerializer(serializers.ModelSerializer):

    class Meta:
        model = Subject
        fields = '__all__'
```

#### 作用: 

`SubjectSerializer`​ 是 Django REST framework 中的 **序列化器**。它的作用是将 Django 模型(在这个例子中是 `Subject`​)转换成 JSON 格式(用于 API 响应), 或者将来自外部的 JSON 数据转换回模型实例(用于 API 请求)。

- **​`ModelSerializer`​**: `SubjectSerializer`​ 继承了 `ModelSerializer`​ 类, 它会自动为 `Subject`​ 模型生成字段(如 `id`​, `name`​, `description`​ 等)。
- **​`Meta`​**​ **类**: 在 `Meta`​ 类中, `model = Subject`​ 表示该序列化器与 `Subject`​ 模型关联, `fields = '__all__'`​ 表示序列化器会包含模型中的所有字段。

#### 用途: 

- **API 请求和响应**: 通常用于将模型数据转化为 JSON 以响应客户端请求, 或者将客户端发送的 JSON 数据转化为模型对象进行保存。
- **序列化**: `SubjectSerializer`​ 将模型对象转换为 JSON 格式(通常用于 API 的响应), 或者将请求的 JSON 数据验证并转换为模型实例(用于 API 的请求处理)。

### 2. **​`SubjectMapper`​**​ **类**(使用自定义的映射器)

```python
class SubjectMapper(ModelMapper):

    class Meta:
        model = Subject
```

#### 作用: 

`SubjectMapper`​ 是一个 **自定义映射器**, 通常用于将 `Subject`​ 模型数据映射到其他数据结构(例如, 字典、DTO、或其他模型对象)中, 或者将数据从其他格式(如字典)映射回模型。

- **​`ModelMapper`​**: `SubjectMapper`​ 继承了 `ModelMapper`​ 类, `ModelMapper`​ 可能是一个你或其他开发人员自定义的类, 用于处理数据映射的操作。
- **​`Meta`​**​ **类**: `Meta`​ 类中的 `model = Subject`​ 表示这个映射器类是专门为 `Subject`​ 模型设计的。

#### 用途: 

- **对象映射**: `SubjectMapper`​ 主要用于将 `Subject`​ 实例与其他数据结构(如视图模型、DTO 或 API 请求数据)之间进行转换。这种映射常用于在应用程序中传递数据或在不同的数据表示之间转换。
- **自定义转换**: `SubjectMapper`​ 允许你在映射过程中做更复杂的操作, 比如合并多个模型字段、修改字段值等。

### 主要区别: 

1. **功能和用途**: 

    - `SubjectSerializer`​ 用于序列化和反序列化模型数据, 特别是与 Django REST framework 配合使用, 处理 API 请求和响应。
    - `SubjectMapper`​ 是自定义的映射器, 用于将模型数据映射到其他数据结构, 或者进行数据转换, 不一定是为了与 API 交互。
2. **自动化程度**: 

    - `SubjectSerializer`​ 使用了 `ModelSerializer`​, 它自动生成与 `Subject`​ 模型相关的字段, 简化了序列化的过程。
    - `SubjectMapper`​ 可能需要更多的自定义配置或逻辑, 用于将数据从一个格式映射到另一个格式。
3. **使用场景**: 

    - `SubjectSerializer`​ 更适用于 API 场景, 进行模型数据的序列化和反序列化操作。
    - `SubjectMapper`​ 更适用于数据转换场景, 可能在应用的内部逻辑中使用, 用于模型和其他数据表示之间的映射。

### 总结: 

- **​`SubjectSerializer`​**​ 是用来序列化和反序列化数据的(通常用于 API), 而 **​`SubjectMapper`​**​ 是用于将数据从一个格式映射到另一个格式的工具, 通常用于其他数据转换场景。
- 它们虽然都涉及到与 `Subject`​ 模型的交互, 但功能和用途有所不同。

‍



# 问答平台项目

创建数据库Tieba

创建配置文件config.py, 放置所有配置代码, 在app.py中导入config.py即可导入所有配置代码

创建扩展文件exts.py, 放置所有扩展代码, 如db模型, 解决循环引用问题

创建models.py, 放置所有模型代码, 如user

引用关系: 

![截屏2025-01-07 17.04.28](../../assets//截屏2025-01-07%2017.04.28-20250107170434-oakmb6u.png)​

```python
# config.py
# 数据库配置信息
HOSTNAME = '127.0.0.1'
PORT = 3306
DATABASE = 'Tieba'
USERNAME = 'root'
PASSWORD = '12345678'
DB_URI = f'mysql+pymysql://{USERNAME}:{PASSWORD}@{HOSTNAME}:{PORT}/{DATABASE}?charset=utf8mb4'
SQLALCHEMY_DATABASE_URI = DB_URI

# exts.py 
from flask_sqlalchemy import SQLAlchemy
db = SQLAlchemy()	# 在exts.py中并不绑定, 创建flask应用后才绑定

# models.py
from exts import db
from datetime import datetime
class UserModel(db.Model):
	__tablename_ = 'user'
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    username = db.Column(db.String(100), nullable=False)
    password = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(100), unique=True, nullable=False)
    join_time = db.Column(db.DateTime, default=datetime.now)

# app.py
from flask import Flask
import config
from exts import db
from models import UserModel

app = Flask(__name__)
# 绑定配置文件
app.config.from_object(config)
# 此处才绑定app, 不用等到创app后才能创db
db.init_app(app)
```

‍

blueprint: 蓝图, 用于模块化操作, 将不同功能拆分为不同的模块

‍

创建python包文件: 

![截屏2025-01-07 20.00.58](../../assets//截屏2025-01-07%2020.00.58-20250107200100-553e3wx.png)

```python
# auth.py, 存放用户登录、授权等相关代码
from flask import Blueprint
bp = Blueprint('auth', __name__, url_prefix='/auth') # 蓝图名为'auth'

@bp.route('/login')
def login():
    pass

# qa.py, 存放问答功能相关代码
from flask import Blueprint
bp = Blueprint('qa', __name__, url_prefix='/') # 根目录即可

@bp.route('/')
def index():
    pass
```

在app.py中只写大框架即可, 绑定蓝图

路由功能放到蓝图中

```python
# app.py
from flask import Flask
import config
from exts import db
from models import UserModel
from blueprint.auth import bp as auth_bp
from blueprint.qa import bp as qa_bp
from flask_migrate import Migrate

app = Flask(__name__)
# app绑定配置文件
app.config.from_object(config)
# SQLAlchemy绑定app
db.init_app(app)
# 绑定迁移工具
migrate = Migrate(app, db)
# app绑定蓝图
app.register_blueprint(auth_bp)
app.register_blueprint(qa_bp)

if __name__ == '__main__':
    app.run()
```

终端运行`flask db init`​、`flask db migrate`​、`flask db upgrade`​

刷新数据库, 可看到新建的表

## 邮箱相关功能

邮箱页面开启STMP服务, 记录授权码

```python
# config.py
# 邮箱配置
MAIL_SERVER = 'smtp.163.com'
MAIL_USE_SSL = True
MAIL_PORT = 465
MAIL_USERNAME = 'KD6_137@163.com'
MAIL_PASSWORD = 'OZEQWQHIUFZSQITR' 	# 授权码
MAIL_DEFAULT_SENDER = 'KD6_137@163.com'

# exts.py
from flask_mail import Mail
mail = Mail()

# app.py
from exts import mail
from flask_mail import Mail
mail.init_app(app)

# auth.py
# 测试函数
@bp.route('/mail/test')
def mail_test():
    message = Message('Test Message',
                      sender='<EMAIL>',
                      recipients=['tisnobler@icloud.com'],
                      body='这是一条测试邮件'
                      )
    mail.send(message)
    return '邮件发送成功'
```

用户输入邮箱后, 向用户邮箱发送验证码

```python
# auth.py
@bp.route('/captcha/email')
def get_email_captcha
```
