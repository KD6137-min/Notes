# Django

## URL

uniform Resource Locator, 统一资源定位符
组成: 

```
scheme://host:port/path/?query-string=xxx#anchor
```

- scheme: 访问的协议, 一般为http或者https以及ftp等
- host: 主机名, 域名
- port: 端口号, HTTP协议是80端口, HTTPS协议是443端口
- path: 查找路径
- query-string: 查询字符串
- anchor: 锚点, 后台一般不用管, 前端用来做页面定位

注意: URL 中的所有字符都是ASCII字符集, 如果出现非ASCII字符, 比如中文, 浏览器会进行编码再进行传输

## 第一个django项目

### 创建项目

- 命令行: `django-admin startproject 项目名`​

  - cd到项目目录后, 创建应用app: `python3 manage.py startapp 应用名`​
  - 一个项目类似于一个架子, 真正起作用的是app

- Pycharm: 还是需要进入命令行单独创建app

### 运行项目

- 命令行: `python3 manage.py runserver [[ip:]port]`​, 可在本地访问网站, 默认端口号8000, 可指定端口号, 指定ip后可使非本地电脑访问网站
- pycharm: 点击绿色箭头运行

### 项目结构

- manage.py: 终端命令均为python3 manage.py xxx, 不能轻易编辑

  - python3 manage.py help: 查看帮助文档
- settings.py: 项目配置
- urls.py: 配置url路由
- wsgi.py: 项目与wsgi协议兼容的web服务器入口, 部署的时候需要用, 一般不改

### project和app的关系

app是project组成部分, 一个app代表项目中的一个模块, 所有url请求的响应都由app处理

### url与视图函数的映射

path与视图函数一一对应

导包: `from django.shortcuts import HttpResponse`​

index()函数应返回HttpResponse对象

urlpatterns列表中, path()完成映射: 

```
urlpatterns = [
	path('', index) 	# path为空时, 执行index函数
]
```

‍

request.GET.get('id')和request.GET['id']区别: 

不存在时, 前者返回none, 后者报错, 前者更安全

在view中写

urls.py需导入views

`path('book', views.book_detail_query_string)`: 不加括号, 回调函数

url两种传参方式:

1. path?id=1
2. path/<int: book_id>: 指定int类型, 若输入非整型, 则404, 否则, 默认是str类型

    ‍

‍CBV和FBV

**CBV(Class-Based Views)**  和 **FBV(Function-Based Views)**  是 Django 中两种不同的视图处理方式, 它们可以**同时存在**于同一个项目中, 甚至在同一个应用中同时使用。

### 1. **CBV(Class-Based Views)**

CBV 是 Django 引入的面向对象的视图方式, 允许你通过定义类来处理 HTTP 请求。CBV 更加灵活, 适合处理复杂的视图逻辑, 且可以通过继承和混入(Mixin)等技术重用代码。

例如: 

```python
from django.http import HttpResponse
from django.views import View

class HelloWorldView(View):
    def get(self, request):
        return HttpResponse("Hello, World!")
```

### 2. **FBV(Function-Based Views)**

FBV 是传统的视图方式, 使用普通的 Python 函数来处理 HTTP 请求。FBV 更加简单直观, 适合处理简单的视图逻辑。

例如: 

```python
from django.http import HttpResponse

def hello_world(request):
    return HttpResponse("Hello, World!")
```

### 3. **CBV 和 FBV 可以同时存在吗？**

- **是的, 它们可以同时存在**。你可以根据需求选择适合的视图方式, FBV 和 CBV 可以在同一个 Django 应用中共存, 不会产生冲突。
- 你可以为某些视图使用 CBV, 而对于其他简单的视图使用 FBV。这取决于视图的复杂性和你的设计需求。

### 4. **混合使用的示例**

假设你有一个应用, 既使用了 FBV 处理简单的请求, 又使用 CBV 处理更复杂的请求。

#### 示例 1: 使用 FBV 和 CBV 处理不同的 URL 路由

```python
# urls.py

from django.urls import path
from .views import hello_world, HelloWorldView

urlpatterns = [
    path('fbv/', hello_world),  # 使用 FBV
    path('cbv/', HelloWorldView.as_view()),  # 使用 CBV
]
```

#### 示例 2: 简单的 FBV

```python
# views.py

from django.http import HttpResponse

def hello_world(request):
    return HttpResponse("Hello, World!")
```

#### 示例 3: 更复杂的 CBV

```python
# views.py

from django.views import View
from django.http import HttpResponse

class HelloWorldView(View):
    def get(self, request):
        return HttpResponse("Hello, World! from CBV!")
```

### 5. **选择 FBV 还是 CBV？**

- **FBV 的优点**: 

    - 简单直接, 适用于简单的请求处理。
    - 易于理解和使用, 尤其是对于新手来说。
- **CBV 的优点**: 

    - 适用于较复杂的视图逻辑, 能够重用代码。
    - 提供了内置的通用视图(例如 `ListView`​, `DetailView`​, `CreateView`​ 等), 使得常见操作(如显示列表、显示单个对象、表单处理等)变得更加方便。
    - 可以通过继承和混入类(Mixin)来扩展和复用功能。

### 总结: 

- **CBV** 和 **FBV** 在同一个 Django 项目中是可以共存的。你可以根据不同的需求选择使用 FBV 还是 CBV, 甚至在同一个应用中同时使用它们。
- 对于简单的视图, 使用 **FBV** 更加方便；对于复杂的视图, 尤其是需要代码复用和更高灵活性的情况, 使用 **CBV** 更加合适。

‍
