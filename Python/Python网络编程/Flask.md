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
