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

​`path('book', views.book_detail_query_string)`​: 不加括号, 回调函数

url两种传参方式:

1. path?id=1
2. path/<int: book_id>: 指定int类型, 若输入非整型, 则404, 否则, 默认是str类型

    ‍

‍
