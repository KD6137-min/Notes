# Python网络编程

`ifconfig`: 查看网卡信息

`ping`: 检查网络是否正常

# 基于HTTP协议的网络资源访问

## requests库

比urllib更方便, 广泛用于Web数据抓取、RESTful API调用等场景, 但高并发场景下不是最优选择, 建议使用异步库, 如`aiohttp`​

- 发送HTTP请求:

  - get请求:

    ```python
    response = requests.get('https://api.example.com/data')
    print(response.text)	# 输出返回的响应内容
    ```
  - post请求:

    ```python
    data = {"key": "value"}
    response = requests.post("https://api.example.com/data", data=data)
    print(response.json())  # 输出返回的 JSON 数据
    ```
- 传递查询参数: 用`params`​参数为请求附加查询参数

  ```python
  params = {"q": "Python", "page": 1}
  response = requests.get("https://api.example.com/search", params=params)
  print(response.url)  # 输出完整 URL
  ```
- 添加请求头: 用`headers`​参数自定义HTTP请求头

  ```python
  headers = {"Authorization": "Bearer your_token", "User-Agent": "my-app"}
  response = requests.get("https://api.example.com/data", headers=headers)
  print(response.status_code)  	# 输出状态码
  ```
- 处理响应:

  - 响应状态码:

    ```python
    print(response.status_code)  # 输出状态码(如 200, 404, 500)
    ```
  - 响应内容:

    ```python
    print(response.text)  		# 原始的文本内容
    print(response.json())  	# JSON 格式的数据
    print(response.content)  	# 原始的二进制数据
    ```
- 处理超时: 为避免请求挂起, 可设置超时时间

  ```python
  response = requests.get("https://api.example.com/data", timeout=5)  # 超时 5 秒
  ```
- 发送文件: 可通过`files`​参数上传文件

  ```python
  files = {"file": open("example.txt", "rb")}
  response = requests.post("https://api.example.com/upload", files=files)
  ```
- 发送JSON数据: 对POST请求可直接发送JSON格式数据

  ```python
  json_data = {"key": "value"}
  response = requests.post("https://api.example.com/data", json=json_data)
  ```
- 使用会话: `requests.Session`​提供会话支持, 可保持跨请求状态(如cookies)

  ```python
  session = requests.Session()
  session.headers.update({"User-Agent": "my-app"})
  
  response = session.get("https://api.example.com/data")
  ```
- 处理Cookies:

  ```python
  response = requests.get("https://api.example.com/data")
  print(response.cookies)  # 输出响应的 Cookies
  
  cookies = {"session_id": "123456"}
  response = requests.get("https://api.example.com/data", cookies=cookies)
  ```
- 使用代理:

  ```python
  proxies = {
      "http": "http://10.10.1.10:3128",
      "https": "https://10.10.1.10:1080",
  }
  response = requests.get("https://api.example.com/data", proxies=proxies)
  ```
- SSL证书验证: `requests`​默认验证SSL证书, 可通过`verify`​参数关闭验证(不推荐)

  ```python
  response = requests.get("https://self-signed.example.com", verify=False)
  ```
- 流式下载大文件: 若下载文件较大, 可使用`stream=True`​

  ```python
  response = requests.get("https://example.com/largefile.zip", stream=True)
  with open("largefile.zip", "wb") as f:
      for chunk in response.iter_content(chunk_size=8192):
          f.write(chunk)
  ```
- 调用REST API:

  ```python
  url = "https://jsonplaceholder.typicode.com/posts"
  response = requests.get(url)
  
  if response.status_code == 200:
      data = response.json()
      for post in data[:5]:  # 只打印前 5 个
          print(f"Title: {post['title']}\nBody: {post['body']}\n")
  else:
      print(f"Request failed with status code {response.status_code}")
  ```

# 基于传输层协议的Socket编程

TCP套接字: socket中type=SOCK_STREAM, 表示TCP套接字

UDP套接字: 适用于强调性能而不是数据完整性的场景, 如传输网络音视频数据

## TCP程序开发流程

![截屏2024-12-27 20.42.47](../../assets//截屏2024-12-27%2020.42.47-20241227204250-q72a9nc.png)![截屏2024-12-28 11.00.05](../../assets//截屏2024-12-28%2011.00.05-20241228110007-3uyky0t.png)​

### **客户端**程序

客户端: 主动发起连接请求

1. 创建客户端套接字对象: `socket.socket(AddressFamily, Type)`:

    - AddressFamily: IP地址类型分IPv4和IPv6, 

      - `socket.AF_INET`: IPv4
      - `socket.AF_INET6`: IPv6
    - type: 传输协议类型

      - `socket.SOCK_STREAM`: TCP套接字
      - `socket.SOCK_DGRAM`: UDP套接字
      - `socket.SOCK_RAW`: 原始套接字
    - socket对象创建时会有发送缓冲区和接收缓冲区

      - `send()`​把数据先写入到发送缓冲区, 由os控制网卡把发送缓冲区的数据发给服务端网卡
      - 由os通过网卡接收数据, 写入接收缓冲区, 应用程序从接收缓冲区获取客户端发送的数据

    ```python
    import socket
    tcp_client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    ```
2. 和服务端套接字建立连接: `connect()`​, 和服务端套接字建立连接, 参数为元组(ip地址, 端口号)
3. 发送数据: `send(''.encoding('utf-8'))`​
4. 接收数据: `recv(大小)`​, 阻塞等待数据到来, 输出数据需要decode()

    - 注意数据要编码解码:

      - 数据编码: `str.encode(encoding='字符集')`​, 字符串->字节码
      - 数据解码: `bytes.decode(encoding='字符集')`​, 字节码->字符串
5. 关闭客户端套接字: `close()`​, 客户端套接字close后, 服务端的recv被阻塞, 返回的数据长度为0, 服务端可通过改长度判断是否已关闭

### **服务端**程序

1. 创建服务端套接字对象

    - 设置端口复用: `tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)`​
2. 绑定IP地址和端口号: `bind()`​, 参数为元组`(ip, port)`​, 省略ip则所有

    - 客户端不需要绑定端口号, 服务端必须绑定(固定, 便于客户端寻找)
3. 设置监听: `listen(n)`​, listen后套接字变为被动套接字, 只负责接收连接请求, 收发消息一般另创套接字负责

    - n: 等待排队连接的最大数量
4. 等待接收客户端连接请求: `accept()`​, 返回元组(用以和客户端通信的socket, 客户端IP地址)

    ```python
    conn_socket, ip_port = tcp_server_socket.accept()
    ```
5. 接收数据: `recv()`​
6. 发送数据: `send()`​
7. 关闭套接字: `close()`​, 要关闭两个, 用于监听的和用于与客户端通信的, 服务端关闭套接字则客户端的recv被阻塞, 返回的数据长度为0

    ```python
    conn_socket.close()
    tcp_server_socket.close()
    ```

### 代码实例

- 简单模型: 

  ```python
  # server.py
  import socket
  
  # 创建 socket 对象
  server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  # 绑定 IP 地址和端口号
  server_socket.bind(('0.0.0.0', 12345))
  # 开始监听
  server_socket.listen(5)
  print("服务器正在监听...")
  
  try:
      while True:
          client_socket, addr = server_socket.accept()
          print(f"连接来自 {addr}")
  
          data = client_socket.recv(1024)
          if data:
              print(f"接收到的数据: {data.decode('utf-8')}")
  
          client_socket.close()
  except KeyboardInterrupt:
      print("\n服务器正在关闭...")
  finally:
      server_socket.close()
      print("服务器已关闭, ")
  
  # -----------------------------------------------------------
  # client.py
  import socket
  
  # 创建 socket 对象
  client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  
  # 连接到服务端
  try:
      client_socket.connect(('192.168.1.2', 12345))  # 替换为实际的服务器 IP 地址
      client_socket.sendall(b"Hello, Server!")
      print("消息已发送")
  except Exception as e:
      print(f"连接失败: {e}")
  finally:
      client_socket.close()
  ```
- 多任务版: 建立连接成功时立刻**创建子线程**处理客户端需求, 不占用主线程

  ```python
  import socket
  import threading
  
  # 处理客户端函数
  def handle_client(conn_socket):
      # 接收数据
      recv_data = conn_socket.recv(1024)
      print('接收到的数据: ', recv_data.decode('utf-8'))
      # 发送数据
      conn_socket.send('客户端, 您的数据我收到了'.encode('utf-8'))
      # 关闭连接
      conn_socket.close()
  
  # 循环接收客户端连接请求
  if __name__ == '__main__':
      # 创建服务端套接字对象
      tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
      # 设置端口复用
      tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
      # 绑定IP地址和端口号
      tcp_server_socket.bind(('', 8888))
      # 设置监听
      tcp_server_socket.listen(128)
      while True:
          # 等待接收客户端连接请求
          conn_socket, ip_port = tcp_server_socket.accept()
          print('客户端地址: ', ip_port)
          # 使用多线程接收多个客户端请求
          sub_thread = threading.Thread(target=handle_client, args=(conn_socket, ))
          sub_thread.start()
  
      tcp_server_socket.close()

## 静态web服务器

![截屏2025-01-19 14.22.43](../../assets//截屏2025-01-19%2014.22.43-20250119142249-hkt3t3g.png)

python自带, 可获取浏览器发送的HTTP请求报文数据, 读取固定页面

在资源所在文件打开终端: `python3 -m http.server [端口号]`, 端口号默认为8000

- 基本结构, 返回固定数据: 

  ```python
  import socket
  if __name__ == '__main__':
      tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
      tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
      tcp_server_socket.bind(('', 8080))
      tcp_server_socket.listen(128)
  
      while True:
          client_socket, addr = tcp_server_socket.accept()
          client_request = client_socket.recv(1024).decode('utf-8')
  
          # 读取路径下文件内容
          with open('./this.txt', 'a') as f:
              f_data = f.read()
  
          # 构建响应报文
          response_line = 'HTTP/1.1 200 OK\r\n'
          response_header = 'Server:pwb\r\n'
          response_body = f_data
          response_data = response_line + response_header + '\r\n' + response_body
          client_socket.send(response_data.encode('utf-8'))
          client_socket.close()

- 动态获取用户请求路径:

  ```python
  import socket
  
  if __name__ == '__main__':
      tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
      tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
      tcp_server_socket.bind(('', 8080))
      tcp_server_socket.listen(128)
      
      while True:
          client_socket, addr = tcp_server_socket.accept()
          client_request = client_socket.recv(1024).decode('utf-8')
  
          # 获取用户请求资源的路径
          request_data = client_request.split(' ')
          request_path = request_data[1]
  
          # 根, 返回首页
          if request_path == '/':
              request_path = '/index.html'
  
          try:
              # 根据请求资源的路径, 读取请求指定文件的数据
              with open('.'+request_path, 'r') as f:
                  f_data = f.read()
          except Exception as e:
              # 请求的文件不存在, 组装404状态的响应报文
              response_line = 'HTTP/1.1 404 Not Found\r\n'
              response_header = 'Server:pwb\r\n'
              response_body = '404 Not Found\r\n'
              response_data = response_line + response_header + '\r\n' + response_body
              client_socket.send(response_data.encode('utf-8'))
          else:
              # 请求成功, 构建响应报文
              response_line = 'HTTP/1.1 200 OK\r\n'
              response_header = 'Server:pwb\r\n'
              response_body = f_data
              response_data = response_line + response_header + '\r\n' + response_body
              client_socket.send(response_data.encode('utf-8'))
          finally:
              client_socket.close()

- 多任务版: 创建子线程

  ```python
  import threading
  import socket
  
  def handle_client_request(client_socket):
      client_request = client_socket.recv(1024).decode('utf-8')
      request_data = client_request.split(' ')
      # 客户端已关闭, 则断开连接
      if len(request_data) == 1:
          client_socket.close()
          return
  
      request_path = request_data[1]
  
      # 根, 返回首页
      if request_path == '/':
          request_path = '/index.html'
  
      try:
          # 读取指定路径下文件
          with open('.' + request_path, 'r') as f:
              f_data = f.read()
      except Exception as e:
          # 构建404状态响应报文
          response_line = 'HTTP/1.1 404 Not Found\r\n'
          response_header = 'Server:pwb\r\n'
          response_body = '404 Not Found\r\n'
          response_data = response_line + response_header + '\r\n' + response_body
          client_socket.send(response_data.encode('utf-8'))
      else:
          # 无异常, 构建200状态响应报文
          response_line = 'HTTP/1.1 200 OK\r\n'
          response_header = 'Server:pwb\r\n'
          response_body = f_data
          response_data = response_line + response_header + '\r\n' + response_body
          client_socket.send(response_data.encode('utf-8'))
      finally:
          client_socket.close()
          
  if __name__ == '__main__':
      tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
      tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
      tcp_server_socket.bind(('', 8080))
      tcp_server_socket.listen(128)
      
      while True:
          client_socket, addr = tcp_server_socket.accept()
          # 创建子线程处理客户端请求, 主线程保持监听
          sub_thread = threading.Thread(target=handle_client_request, args=(client_socket, addr))
          sub_thread.start()

- 面向对象版: 

  ```python
  import threading
  import socket
  
  # 把提供服务的Web服务器抽象为一个类
  class HttpServer:
      # 在初始化方法中创建socket对象
      def __init__(self):
          self.tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
          self.tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
          self.tcp_server_socket.bind(('', 8080))
          self.tcp_server_socket.listen(128)
          
      # 处理客户端请求
      def handle_client_request(self, client_socket):
          client_request = client_socket.recv(1024).decode('utf-8')
          request_data = client_request.split(' ')
          # 客户端已关闭则断开连接
          if len(request_data) == 1:
              client_socket.close()
              return
          request_path = request_data[1]
  
          # 根, 返回首页
          if request_path == '/':
              request_path = '/index.html'
  
          try:
              with open('.' + request_path, 'r') as f:
                  f_data = f.read()
          except Exception as e:
              response_line = 'HTTP/1.1 404 Not Found\r\n'
              response_header = 'Server:pwb\r\n'
              response_body = '404 Not Found\r\n'
              response_data = response_line + response_header + '\r\n' + response_body
              client_socket.send(response_data.encode('utf-8'))
          else:
              response_line = 'HTTP/1.1 200 OK\r\n'
              response_header = 'Server:pwb\r\n'
              response_body = f_data
              response_data = response_line + response_header + '\r\n' + response_body
              client_socket.send(response_data.encode('utf-8'))
          finally:
              client_socket.close()
  
      # 提供开启Web服务器的方法, 让Web服务器处理客户端请求操作
      def start(self):
          while True:
              client_socket, addr = self.tcp_server_socket.accept()
  
              # 连接成功建立, 开辟子线程处理客户端请求
              sub_thread = threading.Thread(target=self.handle_client_request, args=(client_socket, addr))
              sub_thread.start()
  
  if __name__ == '__main__':
      my_server = HttpServer()
      my_server.start()

- 命令行动态绑定端口号: 

  - 获取执行Python程序的终端命令行参数: `print(sys.argv)`

  - 判断参数类型, 设置端口号必须是整形:

      ```python
      if not sys.argv[1].isdigit():
          print('启动命令格式为: python3 xxx.py 9090')
          return
      port = int(sys.argv[1])
      ```

  - 给Web服务器类的初始化方法添加一个端口号参数, 用于绑定端口号:

    ```python
    def __init__(self, port):
    	self.tcp_server_socket.bind(('', port))
    ```

  ```python
  import sys
  import threading
  import socket
  
  class HttpServer:
      def __init__(self, port):
          self.tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
          self.tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
          self.tcp_server_socket.bind(('', port))
          self.tcp_server_socket.listen(128)
          
      # 处理客户端请求
      def handle_client_request(self, client_socket):
          client_request = client_socket.recv(1024).decode('utf-8')
          request_data = client_request.split(' ')
          if len(request_data) == 1:
              client_socket.close()
              return
  
          request_path = request_data[1]
          if request_path == '/':
              request_path = '/index.html'
  
          try:
              with open('./review/' + request_path, 'rb') as f:
                  f_data = f.read()
          except Exception as e:
              response_line = 'HTTP/1.1 404 Not Found\r\n'
              response_header = 'Server:pwb\r\n'
              response_body = '404 Not Found\r\n'
              response_data = response_line + response_header + '\r\n' + response_body
              client_socket.send(response_data.encode('utf-8'))
          else:
              response_line = 'HTTP/1.1 200 OK\r\n'
              response_header = 'Server:pwb\r\n'
              response_body = f_data
              response_data = response_line + response_header + '\r\n' + response_body
              client_socket.send(response_data.encode('utf-8'))
          finally:
              client_socket.close()
  
      def start(self):
          while True:
              client_socket, addr = self.tcp_server_socket.accept()
              sub_thread = threading.Thread(target=self.handle_client_request, args=(client_socket, ))
              sub_thread.start()
  
  def main():
      # 判断参数类型
      if len(sys.argv) != 2:
          print('格式错误: ')
          return
      
      if not sys.argv[1].isdigit():
          print('格式错误: ')
          return
  
      port = int(sys.argv[1])
      my_server = HttpServer(port)
      my_server.start()
  
  if __name__ == '__main__':
      main()

# Web开发

Web框架: 专门负责处理用户的**动态资源请求**, 本质是为web服务器提供服务的应用程序

## WSGI

**Python**的Web服务器网关接口, Web服务器和Web应用框架交互的**标准**, 将Web服务器和Web框架**解耦**, 使服务器和框架不再局限于特定的实现, 使Python Web开发更加灵活和跨平台, 极大地提高了Web开发的兼容性和可移植性

![截屏2025-01-19 14.29.08](../../assets//截屏2025-01-19%2014.29.08-20250119142913-gzrqviq.png)![截屏2024-12-30 17.06.12](../../assets//截屏2024-12-30%2017.06.12-20241230170614-e0z1o2h.png)

左图: 没有WSGI, Web服务器和框架紧密耦合, 只能使用特定的接口和协议

右图: 有WSGI, Web服务器和Web框架解耦, 开发者可选择任意支持WSGI的服务器和框架, 自由组合和替换

## 开发流程

- 创建web框架程序
- 接收web服务器的动态资源请求
- 处理web服务器的动态资源请求并把处理结果返回给web服务器

  - 动态资源判断: 根据资源路径后缀名, `.html`为动态资源请求, 交由Web框架程序处理, 否则为静态资源请求, 交由Web服务器处理

    ```python
    if request_path.endswith(".html"):
    	'''动态资源'''
    	# 构建响应报文
    	response_line = "HTTP/1.1 200 OK\r\n"
    	response_header = "Server:pwd\r\n"
    	response_body = dynamic.frame.application() 	# 需导入自定义模块dynamic
    	response_data = response_line + response_header + "\r\n" + response_body
    
    	client_socket.send(response_data.encode())
    	client_socket.close()
    else:
    	'''静态资源'''
    	try: ...

- web服务器把处理结果组装成响应报文发送给浏览器

![截屏2025-01-19 14.26.37](../../assets//截屏2025-01-19%2014.26.37-20250119142641-vys36p4.png)​

## Web框架程序

- 基础框架: 

  ```python
  # frame.py
  
  # 视图函数
  def index():
  	with open("./template/index.html") as f:
  		file_data = f.read()											# index函数读取模版数据
  	content = file_data.replace("{% content %}", "stock_data", 1) 		# 用数据库实际数据填充模版数据
  	return content
  
  def center():
      with open("./template/center.html") as f:
          file_data = f.read()
      content = file_data.replace("{% content %}", "stock_data", 1)
      return content
  
  def error():
  	return "404 error"
  
  # 接口, 解耦合
  def application():
  	if response_path == "/index.html":
  		return index()
  	elif request_path == "/center.html":
  		return center()
  	else:
  		return error()
  ```
- 使用路由列表, 可使接口函数和业务函数解耦合

  ```python
  # 定义空字典作为路由列表
  func_list = {}
  # 添加数据
  func_list["/index.html"] = index
  func_list["/center.html"] = center
  
  # 接口
  def application(request_path):
  	try:
  		func = func_list[request_path]
  		ret = func()
  		return ret
  	except Exception as e:
  		return error()
  ```
- 使用路由装饰器, 自动添加数据到路由列表

  ```python
  def route(data): 	# data即为路径
  	def func_out(func):
  		func_list[data] = func 	# 路径及其对应的函数添加到路由列表 
  		def func_inner():
  			pass
  		return func_inner
  	return func_out
  
  @route("/index.html") 
  def index():
  	with open("./template/index.html") as f:
  		file_data = f.read()
  	content = file_data.replace("{%content%}", "stock_data", 1)
  	return content
  
  @route("/center.html")
  def center():
      with open("./template/center.html") as f:
          file_data = f.read()
      content = file_data.replace("{%content}", "stock_data", 1)
      return content
  ```
- 连接数据库: 

  ```python
  @route("/index.html")
  def index():
  	with open("./template/index.html") as f:
  		file_data = f.read()
  
  	# 创建数据库连接
  	conn = pymysql.connect(
  		host="localhost", 
  		port=3306, 
  		database="stock", 
  		user="root", 
  		password="12345678", 
  		charset="utf8"
  	)
  
  	# 创建游标
  	cursor = conn.cursor()
  	# 执行sql语句
  	sql = "select * from info;"
  	cursor.execute(sql)
  	stock_data = cursor.fetchall()		# 元组格式
  
  	# 模版
  	template = '''
  		<tr>
  			<td>%s</td>
  			<td>%s</td>
  			<td>%s</td>
  			<td>%s</td>
  			<td>%s</td>
  			<td>%s</td>
  			<td>
  				<input type="button" value="添加" id="toAdd" name="toAdd" systemidvalue="000007"> 
  			</td>
  		</tr>
  	'''
  	html = ''
  
  	# 数据库数据填充到模版中
  	for i in stock_data:
  		html += template % (i[0], i[1], i[2], i[3], i[4], i[5])
  
  	content = file_data.replace("{% content %}", html, 1)
  	cursor.close()
  	conn.close()
  	return content
  ```

‍

# web应用和web服务器的关系

**Web 应用**和**Web 服务器**是 Web 技术中的两个关键组件, 它们在 Web 开发和部署中扮演着不同的角色。理解它们的关系, 有助于你更好地构建、部署和维护 Web 系统。

### 1. **Web 应用**

**Web 应用**(Web Application)是一个通过浏览器或其他客户端通过互联网访问并执行的应用程序。Web 应用是客户端和服务器端之间的一种互动, 用户通过浏览器向服务器发送请求, 服务器根据请求返回响应。Web 应用可以包含用户界面、逻辑处理、数据库交互等。

#### 主要特点: 

- **运行在浏览器中**: Web 应用通常由 HTML、CSS 和 JavaScript 组成, 用户通过浏览器访问。
- **动态内容**: Web 应用通常生成动态的内容(如根据用户输入生成不同的页面内容), 并与后端数据库交互。
- **无缝连接前后端**: Web 应用通常依赖于 Web 服务器提供的接口(如 RESTful API)与后端服务进行数据交换。

#### 例子: 

- 一个在线电商网站, 如 Amazon 或 eBay。
- 社交媒体平台, 如 Facebook 或 Twitter。
- 内容管理系统(CMS)或博客平台, 如 WordPress 或 Ghost。

### 2. **Web 服务器**

**Web 服务器**是用于处理 HTTP 请求并返回 HTTP 响应的硬件或软件。它接收客户端(通常是浏览器)发出的请求, 然后根据请求类型(如静态文件、动态页面等)将相应的资源返回给客户端。

#### 主要功能: 

- **处理 HTTP 请求**: Web 服务器处理来自客户端的请求, 并提供静态内容或将请求转发给动态应用程序。
- **提供静态文件**: Web 服务器可以直接提供静态资源, 如 HTML 文件、CSS、JavaScript 和图片等。
- **反向代理**: Web 服务器可以将某些请求转发到后端的应用服务器或其他服务中进行处理。

#### 常见 Web 服务器: 

- **Apache HTTP Server**: 一个非常流行的开源 Web 服务器。
- **Nginx**: 高性能的 Web 服务器和反向代理服务器, 广泛用于处理大规模的 Web 请求。
- **Microsoft IIS**: 由微软开发的 Web 服务器, 常用于 Windows 环境中。

#### Web 服务器与 Web 应用的关系: 

Web 服务器的作用是接受来自浏览器的请求, 处理请求并返回相应的内容。如果请求的是静态资源(如 HTML 页面、CSS 文件、图片等), Web 服务器直接返回这些资源。如果请求的是动态内容, Web 服务器将请求转发给 Web 应用进行处理, 然后将结果返回给客户端。

### 3. **Web 应用与 Web 服务器的关系**

- **Web 服务器作为中介**: Web 服务器充当客户端和 Web 应用之间的中介。它处理客户端(如浏览器)发来的请求, 决定如何响应这些请求。如果请求的是静态资源(如 HTML 页面), Web 服务器直接返回内容。如果请求的是动态内容(如数据库查询结果或用户登录信息), Web 服务器将请求转发给 Web 应用进行处理。
- **处理请求和响应**: Web 服务器并不直接运行 Web 应用的逻辑, 而是将请求转发给 Web 应用(通常通过某种协议, 如 HTTP、FastCGI 等), 然后将 Web 应用的响应返回给客户端。
- **Web 服务器的工作流程**: 

    1. **客户端发送请求**: 用户在浏览器中输入 URL, 发出请求。
    2. **Web 服务器接收请求**: Web 服务器接收到请求并根据请求的 URL 或文件类型决定如何处理请求。
    3. **Web 服务器处理静态内容**: 如果请求的是静态资源(如图片或 HTML 文件), Web 服务器直接返回这些文件。
    4. **转发到 Web 应用**: 如果请求的是动态内容(如查询数据库), Web 服务器将请求转发给 Web 应用(例如通过 FastCGI、WSGI 或其他方式)。
    5. **Web 应用生成动态响应**: Web 应用根据请求逻辑生成响应, 通常会与数据库或其他后端服务交互。
    6. **Web 服务器返回响应**: Web 应用处理完成后, 将响应返回给 Web 服务器, 后者再将响应返回给客户端。

### 4. **Web 应用与 Web 服务器的交互方式**

Web 服务器和 Web 应用之间的交互通常通过不同的协议或接口实现。常见的有: 

- **CGI(Common Gateway Interface)** : 早期的接口标准, 允许 Web 服务器与外部程序(如 CGI 脚本)进行交互。
- **FastCGI**: 比 CGI 更高效的一种方式, 用于处理动态请求。
- **WSGI(Web Server Gateway Interface)** : 一种标准的 Python Web 应用和 Web 服务器之间的接口, 许多 Python Web 框架(如 Flask、Django)使用它来与 Web 服务器交互。
- **代理(Proxy)** : Web 服务器还可以充当反向代理, 将请求转发到多个 Web 应用或应用服务器。

### 总结: 

- **Web 服务器** 负责接收来自客户端的请求, 提供静态文件, 或将请求转发给 **Web 应用**。
- **Web 应用** 是处理动态内容的程序, 它生成响应并与 Web 服务器进行交互。
- Web 服务器和 Web 应用通常通过标准协议或接口(如 FastCGI、WSGI)进行通信, 协同工作以响应客户端的请求。

‍FastAPI和RESTful API

**FastAPI** 和 **RESTful API** 是两个不同的概念, 它们有不同的含义和应用场景。下面是它们的区别及相互关系: 

### 1. **RESTful API**

**RESTful API**(Representational State Transfer)是一个架构风格, 用于构建基于 HTTP 的 Web 服务。它遵循一些特定的设计原则和约束, 使得 API 能够通过 HTTP 协议高效、可扩展地工作。RESTful API 并不是某种具体的技术或框架, 它只是一个设计哲学, 定义了如何通过标准的 HTTP 方法(如 `GET`​、`POST`​、`PUT`​、`DELETE`​ 等)与 Web 服务交互。

#### 关键特点: 

- **无状态性**: 每个请求都是独立的, 不依赖于之前的请求。每个请求都包含所有信息(如身份验证和数据), 不保留服务器端状态。
- **资源导向**: RESTful API 把每个数据实体或服务视为"资源", 并为其分配 URL。资源可以是用户、订单、产品等。
- **使用 HTTP 方法**: 

    - `GET`: 获取资源。
    - `POST`: 创建资源。
    - `PUT`: 更新资源。
    - `DELETE`: 删除资源。
- **JSON 格式**: 通常通过 JSON 格式传输数据, 但也可以使用 XML 或其他格式。
- **URL 规范化**: 资源的路径通常是简洁的、具有语义的 URL, 例如 `/users/{id}`​ 用于表示用户资源。

#### 示例: 

一个 RESTful API 的例子可能是: 

- `GET /users/1`: 获取 ID 为 1 的用户。
- `POST /users`: 创建一个新用户。
- `PUT /users/1`: 更新 ID 为 1 的用户。
- `DELETE /users/1`: 删除 ID 为 1 的用户。

### 2. **FastAPI**

**FastAPI** 是一个现代的 Web 框架, 专注于快速创建 API(尤其是 RESTful API)。它是基于 Python 编写的, 并使用 Python 的类型提示(type hints)来自动生成文档、校验请求数据和生成 API。

#### 关键特点: 

- **快速**: FastAPI 被设计成一个高性能的框架, 它使用 Python 的 `Starlette`​(用于 Web 服务)和 `Pydantic`​(用于数据验证和解析)库, 因此非常快速, 接近 Go 或 Node.js 等语言的速度。
- **基于标准 Python 类型提示**: FastAPI 依赖 Python 的类型提示(type hints)来自动生成 API 文档和进行数据验证, 简化开发工作并提高代码的可读性和可维护性。
- **自动生成 OpenAPI 文档**: FastAPI 自动为你的 API 生成 **OpenAPI** 和 **Swagger** 文档, 开发者可以通过浏览器界面查看并测试 API。
- **异步支持**: FastAPI 支持异步请求处理, 使其在处理大量并发请求时非常高效。
- **易于使用**: FastAPI 提供了大量的自动化功能, 比如自动验证请求和响应数据、生成文档等, 使得开发 API 变得更加简洁高效。

#### 示例: 

一个简单的 FastAPI 应用程序: 

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/users/{user_id}")
def read_user(user_id: int):
    return {"user_id": user_id}

@app.post("/users/")
def create_user(name: str, age: int):
    return {"name": name, "age": age}
```

### 3. **FastAPI 和 RESTful API 的区别**

- **RESTful API** 是一种架构风格, 而 **FastAPI** 是一个 Web 框架。

    - **RESTful API** 主要描述了如何通过 HTTP 协议进行资源的操作, 而 **FastAPI** 提供了构建这些操作的工具和功能。
- **FastAPI** 可以用来构建 RESTful API。

    - **FastAPI** 实际上是一个实现 **RESTful API** 的工具, 开发者可以使用 FastAPI 来快速创建符合 RESTful 原则的 API。
- **RESTful API** 关注 API 的设计和行为规范, 而 **FastAPI** 关注开发过程中的速度、自动化和数据验证等。
- **FastAPI 的优点**: 

    - 自动生成文档: FastAPI 使用 **OpenAPI** 规范自动生成 API 文档, 开发者不需要手动编写文档。
    - 数据验证: 通过 `Pydantic`​ 和类型提示, FastAPI 能自动验证请求和响应的数据格式。
    - 高性能: FastAPI 使用异步处理方式, 能够高效处理大量请求。
- **RESTful API 的优点**: 

    - 简单直观: RESTful API 的设计遵循 HTTP 协议, 并且通过统一的 URL 路径和 HTTP 方法进行资源的操作, 易于理解和实现。
    - 规范化: RESTful API 的设计强调资源的定义和操作, 能够提供清晰的 API 设计模式, 适合构建分布式系统。

### 总结: 

- **RESTful API** 是一种 Web 服务设计架构, 定义了如何通过 HTTP 操作资源(CRUD 操作)。它是一种标准, 而不是一个具体的框架。
- **FastAPI** 是一个用于创建 Web 应用和 API 的 Python 框架, 专注于性能和易用性, 可以用来快速创建 **RESTful API**。
- **关系**: FastAPI 是一种实现 **RESTful API** 的工具, 你可以使用 FastAPI 来构建符合 RESTful 架构的 API 服务。

‍

---

‍

# asgi和wsgi

**ASGI**(Asynchronous Server Gateway Interface)和 **WSGI**(Web Server Gateway Interface)是 Python 中两种不同的 Web 应用接口标准, 它们都用于定义 Web 服务器与 Web 应用之间的交互方式。两者的主要区别在于它们如何处理请求和响应, 尤其是在处理异步操作时的能力上。

### 1. **WSGI(Web Server Gateway Interface)**

**WSGI** 是 Python 的一种同步 Web 服务器接口标准, 最初由 PEP 333(Python Enhancement Proposal)提出。它定义了 Web 服务器如何与 Python 应用程序通信, 尤其是在处理 HTTP 请求和响应时的交互。

#### 关键特点: 

- **同步(Blocking)** : WSGI 基于同步模型, 每个请求会阻塞当前的工作线程, 直到该请求完全处理完成。这意味着在处理请求时, 服务器无法同时处理其他请求, 必须等待当前请求完成。
- **阻塞操作**: 当 Web 应用进行 I/O 操作(如数据库查询、文件读取、网络请求等)时, 它会阻塞当前线程, 直到操作完成。
- **简单和成熟**: WSGI 是 Python Web 开发中的标准接口, 广泛支持并已应用于多个流行的 Web 框架, 如 Django、Flask 等。

#### WSGI 典型工作流程: 

1. 用户的 HTTP 请求首先由 Web 服务器接收。
2. Web 服务器将请求传递给 Python 应用程序(WSGI 应用), 通常是一个函数。
3. WSGI 应用处理请求并返回响应。
4. Web 服务器将响应返回给客户端。

#### 示例(WSGI 应用): 

```python
def simple_app(environ, start_response):
    # 获取请求的路径
    path = environ.get('PATH_INFO', '/')
    
    # 设置响应头
    start_response('200 OK', [('Content-type', 'text/plain')])
    
    # 返回响应内容
    return [f'Hello, WSGI! You requested {path}'.encode('utf-8')]
```

### 2. **ASGI(Asynchronous Server Gateway Interface)**

**ASGI** 是 WSGI 的异步扩展, 专为支持异步编程模型而设计。ASGI 是由 PEP 484 提出的, 旨在解决 WSGI 无法有效处理长时间 I/O 操作(如 WebSockets 和长轮询等)的问题。ASGI 支持异步和同步操作, 并能够处理并发连接。

#### 关键特点: 

- **异步(Non-blocking)** : ASGI 支持异步 I/O 操作, 允许应用程序在等待 I/O 操作时处理其他请求。这样, 应用程序可以在执行某些操作时不阻塞其他请求, 极大提高了性能, 尤其是在高并发情况下。
- **支持 WebSockets 和其他协议**: ASGI 使得 Web 应用可以处理不仅限于 HTTP 请求的连接, 如 WebSockets、Server-Sent Events(SSE)等, 适合构建实时应用。
- **灵活性**: ASGI 支持同时使用同步和异步代码, 允许开发者根据需求选择合适的方式处理请求。
- **异步服务器**: ASGI 需要一个异步的 Web 服务器(如 Daphne、Uvicorn)来发挥其优势。

#### ASGI 典型工作流程: 

1. 用户的 HTTP 请求或其他协议请求(如 WebSocket)由 Web 服务器接收。
2. ASGI 应用程序处理请求, 异步执行任何 I/O 操作。
3. 服务器将响应返回给客户端, 支持实时更新等功能。

#### 示例(ASGI 应用): 

```python
import asyncio

async def simple_app(scope, receive, send):
    # scope 包含有关连接的元数据(如请求类型、协议等)
    # 使用 async/await 进行异步处理
    await send({
        'type': 'http.response.body',
        'body': b'Hello, ASGI!',
    })
```

### 3. **WSGI 和 ASGI 的主要区别**

| 特性             | WSGI                                    | ASGI                                      |
| ---------------- | --------------------------------------- | ----------------------------------------- |
| **请求处理模型** | 同步(Blocking)                          | 异步(Non-blocking)                        |
| **并发处理能力** | 单线程同步, 每次只能处理一个请求        | 异步多线程, 可以同时处理多个请求          |
| **支持协议**     | 仅支持 HTTP 协议                        | 支持 HTTP、WebSocket、SSE、GraphQL 等协议 |
| **适用场景**     | 传统的 Web 应用(如 Django、Flask)       | 实时应用(如 WebSockets)、高并发场景       |
| **服务器支持**   | 支持的 Web 服务器有 Gunicorn、uWSGI 等  | 需要异步服务器, 如 Daphne、Uvicorn 等     |
| **性能**         | 对于长时间 I/O 操作(如数据库查询)不理想 | 高并发, 适合长时间 I/O 操作, 实时应用等   |
| **代码示例**     | 以函数形式处理请求                      | 以异步函数处理请求                        |

### 4. **何时使用 WSGI 和 ASGI**

- **使用 WSGI**: 

    - 如果你的应用是一个传统的 Web 应用, 主要处理短时间的 HTTP 请求/响应, 且不需要实时通信或并发支持。
    - 例如, 传统的 Django、Flask 等应用, 它们通常是基于 WSGI 运行的。
- **使用 ASGI**: 

    - 如果你的应用需要高并发处理, 或需要处理实时功能, 如 WebSockets、服务器推送(SSE)、长轮询等。
    - 例如, 使用 Django 3.0+ 或 FastAPI 等现代框架构建实时应用或高并发系统时, 可以使用 ASGI。

### 5. **总结**

- **WSGI** 是传统的 Web 应用标准接口, 适用于同步请求/响应模型。
- **ASGI** 是 WSGI 的异步扩展, 支持高并发、异步 I/O 操作和实时协议(如 WebSockets)。
- **ASGI** 在需要高性能和高并发的现代 Web 应用中更加适用, 尤其是在实时应用和 WebSockets 等场景中。

简而言之, **WSGI** 适用于传统的同步 Web 应用, 而 **ASGI** 更适合处理现代异步应用、实时通信和高并发场景。

# rfile和wfile

在 Python 中, `StreamRequestHandler`​ 是 `BaseHTTPRequestHandler`​ 类的子类, 通常用于处理 HTTP 请求。`rfile`​ 和 `wfile`​ 是 `StreamRequestHandler`​ 类的两个重要属性, 分别代表请求的输入流和响应的输出流。

### `rfile`​

- **定义**: `rfile`​ 是一个用于读取请求内容的文件-like 对象, 它提供了读取客户端请求的功能。客户端发送的请求数据(如 POST 请求的正文)会通过 `rfile`​ 读取。
- **用途**: 你可以通过 `rfile`​ 读取请求体的内容。例如, 在处理 POST 请求时, `rfile`​ 中可能包含了用户提交的表单数据或者文件。

### `wfile`​

- **定义**: `wfile`​ 是一个用于向客户端发送响应的文件-like 对象, 它允许你将响应写回客户端。你可以通过 `wfile`​ 将 HTTP 响应的内容(如状态码、头部和响应体)发送给客户端。
- **用途**: 在处理请求后, 你可以通过 `wfile`​ 写入响应的内容。例如, 返回一个 HTML 页面或 JSON 数据给客户端。

### 示例代码

下面是一个简单的例子, 展示了如何使用 `rfile`​ 和 `wfile`​ 来处理 HTTP 请求: 

```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class MyHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        # 响应客户端请求
        self.send_response(200)  # 状态码 200 OK
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        
        # 通过 wfile 写入响应内容
        self.wfile.write(b"Hello, world!")
    
    def do_POST(self):
        # 读取请求数据
        content_length = int(self.headers['Content-Length'])  # 获取请求体的长度
        post_data = self.rfile.read(content_length)  # 读取请求体数据
        
        # 打印 POST 请求的内容
        print(f"Received POST data: {post_data.decode('utf-8')}")
        
        # 发送响应
        self.send_response(200)  # 状态码 200 OK
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        self.wfile.write(b"POST request received")

# 设置服务器
server_address = ('', 8080)
httpd = HTTPServer(server_address, MyHandler)
print("Starting server on port 8080...")
httpd.serve_forever()
```

### 解释: 

1. **​`do_GET`​**​ **方法**: 处理 GET 请求时, 使用 `wfile`​ 将响应内容("Hello, world!")发送回客户端。
2. **​`do_POST`​**​ **方法**: 处理 POST 请求时, 使用 `rfile`​ 读取请求的正文数据, 打印出来, 并通过 `wfile`​ 发送一个简单的响应回客户端。

### 总结

- **​`rfile`​**: 读取请求数据(例如 POST 请求的正文)。
- **​`wfile`​**: 写入响应数据(例如返回的 HTML 或 JSON 数据)。

这些属性让你能够处理 HTTP 请求和响应的输入输出流。

‍
