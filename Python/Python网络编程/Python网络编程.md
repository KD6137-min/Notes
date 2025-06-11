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
  ```

## 静态web服务器

![截屏2025-01-19 14.22.43](../../assets//截屏2025-01-19%2014.22.43-20250119142249-hkt3t3g.png)​

python自带, 可获取浏览器发送的HTTP请求报文数据, 读取固定页面

在资源所在文件打开终端: `python3 -m http.server [端口号]`​, 端口号默认为8000

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
  ```
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
  ```
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
  ```
- 面向对象版: 

  ```python
  ‍import threading
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
  ```
- 命令行动态绑定端口号: 

  - 获取执行Python程序的终端命令行参数: `print(sys.argv)`​
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

  ```

# Web开发

Web框架: 专门负责处理用户的**动态资源请求**, 本质是为web服务器提供服务的应用程序

## WSGI

**Python**的Web服务器网关接口, Web服务器和Web应用框架交互的**标准**, 将Web服务器和Web框架**解耦**, 使服务器和框架不再局限于特定的实现, 使Python Web开发更加灵活和跨平台, 极大地提高了Web开发的兼容性和可移植性

![截屏2025-01-19 14.29.08](../../assets//截屏2025-01-19%2014.29.08-20250119142913-gzrqviq.png)![截屏2024-12-30 17.06.12](../../assets//截屏2024-12-30%2017.06.12-20241230170614-e0z1o2h.png)​

左图: 没有WSGI, Web服务器和框架紧密耦合, 只能使用特定的接口和协议

右图: 有WSGI, Web服务器和Web框架解耦, 开发者可选择任意支持WSGI的服务器和框架, 自由组合和替换

## 开发流程

- 创建web框架程序
- 接收web服务器的动态资源请求
- 处理web服务器的动态资源请求并把处理结果返回给web服务器

  - 动态资源判断: 根据资源路径后缀名, `.html`​为动态资源请求, 交由Web框架程序处理, 否则为静态资源请求, 交由Web服务器处理

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
    ```
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
