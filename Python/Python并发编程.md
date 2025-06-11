# Python并发编程

# 概念

- 进程: 一个运行的程序, 资源分配和调度的基本单位

  - 主进程: 进程一旦运行, 自动创建主进程, 在主进程中创建的都是子进程, 进程间**不共享全局变量**, 创建子进程会对主进程的资源进行**拷贝**, 使子进程拥有同名但是不同的变量, 主进程和子进程互相独立
  - 进程的结束顺序: (默认)主进程会等待所有子进程执行结束再结束
- 线程: 线程是程序执行的最小单位, 进程是线程的容器, 线程不自己拥有资源, 与同进程的线程**共享全部资源**

  - 线程体: 线程要执行的代码, 会被封装到一个函数中
  - 主线程: Python程序至少有一个线程, 即主线程, 由Python解释器在程序启动后自动创建主线程, 程序结束后停止主线程, 多线程中, 主线程负责其他线程(子线程)的启动、挂起、停止等, 也包含用户代码
  - 线程的执行顺序: 无序, 由cpu决定调度
  - 线程的结束顺序: (默认)主线程会等待所有子线程结束后再结束
- 进程和线程对比:

  - 线程依附于进程, 线程不能独立执行, 线程之间共享全局变量
  - 进程默认提供一线程, 进程可创建多个线程, 不共享全局变量, 创建进程的资源开销比线程大
- 多任务: 同一时间内执行多个任务, 表现形式为并发、并行, 实现多任务: 多进程、多线程

  - 使用多线程的情况: 

    - 程序需要维护许多共享的状态(尤其是可变状态), Python中的列表、字典、集合都是线程安全的, 所以使用线程而不是进程维护共享状态的代价相对较小
    - 程序会花费大量时间在I/O操作上, 没有太多并行计算的需求且不需占用太多的内存
  - 使用多进程的情况: 

    - 程序执行计算密集型任务(如: 字节码操作、数据处理、科学计算)
    - 程序的输入可以并行的分成块, 并且可以将运算结果合并
    - 程序在内存使用方面没有任何限制且不强依赖于I/O操作(如读写文件、套接字等)

# 锁

## 全局解释器锁GIL

Python通过全局解释器锁GIL(Global Interpreter Lock)来实现线程同步

- Python程序只有单线程时, 并不会启用GIL, 而当用户创建了一个thread表示要使用多线程时, Python解释器就会自动激活GIL, 并创建所需要的上下文环境和数据结构
- Python字节码解释器的工作原理是按照指令的顺序一条一条地顺序执行, Python内部维护着一个数值n, 即Python内部时钟, Python在执行了N条指令以后应该立即启动线程调度机制,  可通过`sys.getcheckinterval()`​获取n
- 获取到GIL的线程就能开始执行, 而其他线程则**必须等待**, Python的多线程不能发挥CPU的多核优势, 性能十分低下, 建议使用多进程

## 互斥锁

线程共享全局变量, 需要同步, 即保证同一时刻只能有一个线程操作全局变量(临界资源)

- `threading.Lock`: 常规锁, 适用于不需要在同一线程中多次获取锁的场景

  - 不可重入: 一线程在持有锁的情况下, 不能再次获取该锁, 否则会死锁
  - `Lock()`​创建锁、`.acquire()`​上锁、`.release()`​释放锁
- `threading.RLock`: 递归锁, 允许同一个线程多次获取锁而不造成死锁, 内部维持一个计数器, 为0时完全释放锁, 适用于需要在同一线程中递归地多次获取锁的场景

  ```python
  import threading
  
  rlock = threading.RLock()
  
  def recursive_task(n):
      rlock.acquire()
      print(f"Recursion level {n}")
      if n > 0:
          recursive_task(n - 1)  # 递归调用
      rlock.release()
  
  recursive_task(3)
  ```
- 死锁: 一直等待对方释放锁, 造成应用程序停止响应, 要注意在适当的地方释放锁, 结合try和finally

# 多进程

有效解决GIL不能发挥CPU多核特性问题, 适用于计算密集型任务

```python
from multiprocessing import Process
# 创建进程对象
进程对象 = Process(target, args=(...), kwargs={'': ''}
# target: 执行的目标任务名, 函数名/方法名, 不能加括号
# name: 进程名, 一般不用设置
# group: 进程组
# args: 以元组形式传参, 传参和参数顺序保持一致
# kwargs: 以字典形式传参, key必须和参数名一致
```

- 启动进程: `进程对象.start()`​
- 获取进程编号: `os.getpid()`​, 需import os
- 获取父进程编号: `os.getppid()`​
- `join()`: 等待进程执行结束
- 设置守护主进程, 主进程退出直接销毁子进程, 不再执行子进程代码: `‍work_process.daemon = True`​

- 显式销毁子进程: `work_process.terminate()`​

进程通信: 使用`multiprocessing.Queue`​类, 基于管道和锁提供多进程共享队列

进程池: `concurrent.futures.ProcessPoolExecutor()`​, 可使用with语句管理, 方法类似于线程池

```python
import concurrent.futures

with concurrent.futures.ProcessPoolExecutor() as executor:
	for number, prime in zip(PRIMES, executor.map(is_prime, PRIMES)):
		print('%d is prime: %s' % (number, prime))
```

# 多线程

```python
from threading import Thread, Lock
# 创建线程对象
线程对象 = Thread(target=任务名)
# target: 函数名/方法名, 不带括号
# name: 线程名, 一般不设置
# group: 线程组
# args元组传参、kwargs字典传参
```

- 设置守护主线程, 主线程退出则子线程销毁: `work_thread = Thread(target, daemon=True)`​
- 获取当前线程信息: `current_thread = threading.current_thread()`​
- `active_count()`: 返回当前处于活动状态的线程个数
- `current_thread()`: 返回当前的Thread对象
- `main_thread()`: 返回主线程对象, 主线程是Python解释器启动的线程
- `join(timeout=None)`: 当前线程中调用子线程join方法, 会阻塞当前线程, 等待子线程执行完毕, 确保资源正确释放, timeout用于设置超时时间, 单位秒, 若不设置可一直等待到结束, 子线程不需要join也能完成
- `start()`: 创建一个新线程并使线程进入就绪态, 由操作系统调度执行

  - 只能调用一次
  - 主线程在子线程start后立即继续执行而不需等子线程完成工作, 即主线程与子线程并行(异步), 主线程退出后, 子线程会继续执行直到完成

  ```python
  import threading
  
  print(threading.active_count())
  t = threading.current_thread()
  m = threading.main_thread()
  print(t.name)
  print(m.name)
  ```

## 线程体的两种实现方式

- 自定义函数实现线程体: 要注意给每个子线程执行的机会, 只有当前线程休眠, 其他线程才有机会执行, 若无休眠, 则在第1个子线程执行完毕后再执行第2个子线程

  ```python
  def thread_body():
      t = threading.current_thread()
      for n in range(5):
          print('第{0}次执行线程{1}'.format(n, t.name))
          time.sleep(2)
      print('线程{0}执行完成!'.format(t.name))
  
  t1 = threading.Thread(target=thread_body)
  t2 = threading.Thread(target=thread_body, name='MyThread')
  t1.start() 
  t2.start()
  ```
- 自定义线程类实现线程体: 创建一个Thread子类(需继承Thread类)并重写`run()`​方法(即线程体函数)

  ```python
  class SmallThread(Thread):
      def __init__(self, name=None):
          super().__init__(name=name)
  
      def run(self):   	# 重写父类的run()方法
          t = threading.current_thread()
          for n in range(5):
              print('第{0}次执行线程{1}'.format(n, t.name))
              time.sleep(2)
          print('线程{0}执行完成!'.format(t.name))
  
  t1 = SmallThread()
  t2 = SmallThread(name='MyThread')
  t1.start()
  t2.start()



## 线程管理

### 等待线程结束

a线程等待b线程执行结束后才能继续执行: 
![截屏2024-12-24 00.26.52](./Python并发编程.assets/截屏2024-12-24 00.26.52-20241224002655-fek1ofj.png)

`join(timeout=None)`:

  ```python
import threading
import time
value = []				# 定义共享变量value, 多线程都可访问
def thread_body():
	print('t1子线程开始……')
	for n in range(2):
		print('t1子线程执行……')
		value.append(n)		# 子线程体中修改value的内容
		time.sleep(2)
	print('t1子线程结束, ')

print('主线程开始执行……')
t1 = threading.Thread(target=thread_body)
t1.start()	# 启动线程t1
t1.join()	# 主线程被阻塞, 等待t1线程结束
print('value = {0}'.format(value))
print('主线程继续执行……')
  ```

### 线程停止

线程体结束时, 线程即停止, 常在线程体中执行一死循环, 并通过停止变量来控制

```python
import threading
import time

isrunning = True		# 创建线程停止变量, 控制线程结束

def workthread_body():
	while isrunning:	# 工作线程体死循环
		print('工作线程执行中……')
		time.sleep(5)
	print('工作线程结束')

def controlthread_body(): 	# 控制线程体从控制台读取指令, 根据指令修改线程停止变量
	global isrunning		# 需要在线程中修改isrunning, 故声明为全局
	while isrunning:		# 控制线程体死循环
		command = input('请输入停止指令:  ')
		if command == 'exit':
			isrunning = False
			print('控制线程结束')

workthread = threading.Thread(target=workthread_body)
workthread.start()

controlthread = threading.Thread(target=controlthread_body)
controlthread.start()
```

### 线程池

Thread Pool, 一种高效的多线程管理机制, 通过维护一个线程的集合来复用线程资源, 限制并发数量, 减少频繁创建和销毁线程的开销, 并通过统一接口提交任务, 无需手动管理线程生命周期

应用场景:

- IO密集型任务: 特别适合线程池
- 任务队列: 大量小任务需执行时

注意:

- 避免阻塞主线程: 线程池运行的任务如果出现长时间阻塞, 可能会阻碍其他任务的执行
- 任务异常处理: 使用`future.result()`​时, 任何未捕获的异常都会在主线程抛出, 需要进行异常处理
- 线程池资源管理: 推荐使用`with`​语句管理线程池, 确保在任务完成后正确释放资源

  - `with ThreadPoolExecutor(max_workers=3) as executor:`​

主要实现方式:

- `concurrent.futures.ThreadPoolExecutor`: 推荐, 接口简单, 功能强大

  - 构造方法: `ThreadPoolExecutor(max_workers=None, thread_name_prefix='', initializer=None, initargs=())`​

    - `max_workers`: 线程池的最大线程数, 默认为`os.cpu_count() * 5`​, 若设置为`None`​, 线程池将无限增长
    - `thread_name_prefix`: 线程名前缀, 方便调试时区分线程
    - `initializer`: 初始化函数, 在每个线程启动时调用一次
    - `initargs`: 传递给`initializer`​的参数, 必须是元组

    ```python
    from concurrent.futures import ThreadPoolExecutor
    
    def init_func():
        print("Thread initialized")
    
    pool = ThreadPoolExecutor(max_workers=3, thread_name_prefix='MyThread', initializer=init_func)
    ```
  - `submit(fn, *args, **kwargs)`: 向线程池提交一个任务, 返回一个`Future`​对象(该任务的异步执行结果)

    - `fn`: 要执行的函数
    - `*args`​和`**kwargs`: 传递给函数的参数

    ```python
    def task(x):
        return x * x
    
    future = pool.submit(task, 5)
    print(future.result())  # 输出 25
    ```
  - `map(func, *iterables, timeout=None, chunksize=1)`: 将一个函数映射到可迭代对象上, 并并行执行, 返回一个迭代器, 按输入顺序返回结果

    - `func`: 要执行的函数
    - `iterables`: 一个或多个可迭代对象
    - `timeout`: 超时时间, 超时会抛出`TimeoutError`​
    - `chunksize`: 任务分块大小, 默认为1, 仅在`ProcessPoolExecutor`​中有效

    ```python
    def square(x):
        return x * x
    
    results = pool.map(square, range(5))
    print(list(results))  # 输出 [0, 1, 4, 9, 16]
    ```
  - `shutdown(wait=True, cancel_futures=False)`: 关闭线程池, 释放资源

    - `wait`: 是否等待所有线程完成执行, 默认为`True`​
    - `cancel_futures`: 是否取消所有未开始的任务, 默认为`False`​
  - 属性:

    - `_threads`: 存储当前线程池中所有活动线程的集合, 让线程池可以跟踪正在运行的线程, 以便管理
    - `_work_queue`: 任务队列, 存储待执行的任务, 提交时任务放入该队列
  - `Future`​对象: 表示一个异步执行的操作, 可用于获取异步操作的结果/状态、处理异常

    - `.result(timeout=None)`: 获取任务的返回值, 任务未完成则等待, 超时则抛出`concurrent.futures.TimeoutError`​
    - `.done()`: 检查任务是否完成
    - `.cancel()`: 尝试取消任务, 任务已经运行则无法取消
    - `add_done_callback(fn)`: 注册一个回调函数, 任务完成时触发该回调

      ```python
      loop = asyncio.get_event_loop()
      future = asyncio.gather(prime_filter(2, 100), square_mapper(1, 100))
      future.add_done_callback(lambda x: print(x.result()))
      ```
  - 代码示例:

    ```python
    from concurrent.futures import ThreadPoolExecutor
    
    def task(x):
        print(f"Task {x} started")
        return x * x
    
    # 创建线程池
    with ThreadPoolExecutor(max_workers=3) as executor:
        # 提交单个任务
        future = executor.submit(task, 5)
        print(f"Result of task 5: {future.result()}")
    
        # 使用map提交多个任务
        results = executor.map(task, range(5))
        print(f"Results: {list(results)}")
    
    	# 使用列表推导式提交多个任务
    	# futures = [executor.submit(task, i) for i in range(5)]
        # 获取结果
        # for future in futures:
            # print(future.result())
    ```

    ```python
    import time
    import threading
    from concurrent.futures import ThreadPoolExecutor


    class Account(object):
        """银行账户"""
    
        def __init__(self):
            self.balance = 0.0
            self.lock = threading.Lock()
    
        def deposit(self, money):
            # 通过锁保护临界资源
            with self.lock:
                new_balance = self.balance + money
                time.sleep(0.001)
                self.balance = new_balance


    def main():
        """主函数"""
        account = Account()
        # 创建线程池
        pool = ThreadPoolExecutor(max_workers=10)
        futures = []
        for _ in range(100):
            future = pool.submit(account.deposit, 1)
            futures.append(future)
        # 关闭线程池
        pool.shutdown()
        for future in futures:
            future.result()
        print(account.balance)


    if __name__ == '__main__':
        main()
    ```
- `multiprocessing.pool.ThreadPool`: 提供了类似的线程池实现, 主要用于与`multiprocessing`​保持一致的接口风格

  ```python
  from multiprocessing.pool import ThreadPool
  import time
  
  def task(name):
      print(f"Task {name} started")
      time.sleep(2)
      print(f"Task {name} finished")
      return f"Result of {name}"
  
  # 创建线程池, 最大线程数为3
  pool = ThreadPool(3)
  
  # 使用map提交任务, 将输入列表的每个元素映射到任务函数中, 并返回结果列表
  # 不支持submit这种更细粒度的接口
  results = pool.map(task, range(5))
  
  print("All tasks completed:")
  print(results)
  ```

### 线程调度

#### `threading.Condition`​类

基于**生产者-消费者**模型的线程同步工具, 通过条件变量, 在多个线程间传递信号, 协调线程行为(等待或唤醒)

常与`acquire()`​和`release()`​锁方法一起使用, 以确保在操作条件变量时是线程安全的

可用with语句进行上下文管理: `with self.condition:`​, 自动调用`self.condition.acquire()`​获取与该`Condition`​对象关联的锁, 若没有显式传递锁, 则创建一个内部锁, 并在此时自动获取锁

- 构造方法: `threading.Condition(lock=None)`​, `lock`​可选锁对象, 默认为`None`​, 不指定时会使用内部`_lock`​对象
- `wait(timeout=None)`: 使当前线程等待直到满足某个条件

  - 在`Condition`​上等待, 直到另一个线程调用`notify()`​或`notify_all()`​
  - `timeout`: 可选, 指定最大等待时间, 超时则抛出`TimeoutError`​

- `notify(n=1)`: 唤醒n个正在等待的线程(被`wait()`​方法阻塞的), 默认1个
- `notify_all()`: 唤醒所有正在等待的线程

```python
import threading
import time
import random

class ProducerConsumer:
    def __init__(self, size):
        self.buffer = []  # 缓存区
        self.size = size  # 缓存区的最大大小
        self.condition = threading.Condition()

    def producer(self):
        while True:
            # 生产数据
            item = random.randint(1, 100)
            with self.condition: 
                while len(self.buffer) == self.size:  # 缓存区满
                    print("Buffer is full, producer is waiting...")
                    self.condition.wait()  # 等待消费者消费
                self.buffer.append(item)  # 生产一个商品
                print(f"Produced: {item}")
                self.condition.notify_all()  # 通知消费者

    def consumer(self):
        while True:
            with self.condition:  # 获取锁
                while not self.buffer:  # 缓存区为空
                    print("Buffer is empty, consumer is waiting...")
                    self.condition.wait()  # 等待生产者生产
                item = self.buffer.pop(0)  # 消费一个商品
                print(f"Consumed: {item}")
                self.condition.notify_all()  # 通知生产者

def main():
    pc = ProducerConsumer(size=5)

    # 创建生产者和消费者线程
    producer_thread = threading.Thread(target=pc.producer)
    consumer_thread = threading.Thread(target=pc.consumer)

    producer_thread.start()
    consumer_thread.start()

    producer_thread.join()
    consumer_thread.join()

if __name__ == "__main__":
    main()
```

存钱&取钱: 

```python
from concurrent.futures import ThreadPoolExecutor
from random import randint
from time import sleep
import threading


class Account:
    """银行账户"""

    def __init__(self, balance=0):
        self.balance = balance
        lock = threading.RLock()
        self.condition = threading.Condition(lock)

    def withdraw(self, money):
        """取钱"""
        with self.condition:
            while money > self.balance:
                self.condition.wait()
            new_balance = self.balance - money
            sleep(0.001)
            self.balance = new_balance

    def deposit(self, money):
        """存钱"""
        with self.condition:
            new_balance = self.balance + money
            sleep(0.001)
            self.balance = new_balance
            self.condition.notify_all()


def add_money(account):
    while True:
        money = randint(5, 10)
        account.deposit(money)
        print(threading.current_thread().name, 
              ':', money, '====>', account.balance)
        sleep(0.5)


def sub_money(account):
    while True:
        money = randint(10, 30)
        account.withdraw(money)
        print(threading.current_thread().name, 
              ':', money, '<====', account.balance)
        sleep(1)


def main():
    account = Account()
    with ThreadPoolExecutor(max_workers=15) as pool:
        for _ in range(5):
            pool.submit(add_money, account)
        for _ in range(10):
            pool.submit(sub_money, account)


if __name__ == '__main__':
    main()
```

# 异步编程

## 概念

- 事件循环Event Loop: 调度机制, 用于管理异步任务并调度协程的执行

  - 事件驱动模型: 通用设计模式, 通过**事件循环**来管理任务(不断检查事件队列中事件), 当事件发生时(如I/O完成、用户输入), 相应的事件处理器被触发, 常依赖回调机制, 所有操作都是非阻塞的, 通过事件触发下一步动作, 不限于单线程
- 协程Coroutine: 可暂停和恢复执行的特殊函数, 如await和yield, 实现异步的一种高级抽象
- 异步: 从调度程序的任务队列中挑选任务, 不保证以某种顺序执行

  - 异步I/O: I/O操作(如文件读写、网络请求)是非阻塞的, 当I/O完成时触发回调通知主线程
  - 异步I/O + 单线程模型: 事件驱动模型的实现之一, 单线程利用异步实现多任务, 用协程切换任务, 不够直观, 易陷入'回调地狱', 多核CPU上可运行与核心数相同数量的进程数, 适用于高并发I/O密集型场景, 如Web服务器、爬虫

## `asyncio`​模块

基于协程, 通过**事件循环**机制来调度任务和协程, 用异步处理并发, 在**单线程**中执行多个任务, 而无需创建多个线程或进程

- `loop`​类: 事件循环

  - `loop.create_task(coroutine)`: 将协程包装为task并调度到事件循环中
  - `loop.run_until_complete(future)`: 运行直到指定的future/协程完成, 并返回结果
  - `loop.run_forever()`: 持续运行事件循环, 直到显式调用`loop.stop()`​
  - `loop.stop()`: 停止事件循环
  - `loop.is_running()`: 返回事件循环是否正在运行
  - `loop.call_later(delay, callback, *args)`: 在指定的延迟时间后调用回调函数
  - `loop.call_at(when, callback, *args)`: 在指定的绝对时间点调用回调函数
  - `loop.call_soon(callback, *args)`: 尽快(但不立即)调用回调函数
  - `loop.time()`: 返回事件循环的内部时间(高精度时钟)
  - `loop.create_connection(protocol_factory, host, port, **kwargs)`: 创建TCP连接
  - `loop.create_server(protocol_factory, host=None, port=None, **kwargs)`: 创建TCP服务器
  - `loop.run_in_executor(executor, func, *args)`: 在线程池或进程池中运行阻塞的同步代码
  - `loop.add_reader(fd, callback, *args)`: 注册文件描述符(`fd`​)的可读事件
  - `loop.add_writer(fd, callback, *args)`: 注册文件描述符(`fd`​)的可写事件
  - `loop.remove_reader(fd)`: 移除文件描述符的可读事件
  - `loop.remove_writer(fd)`: 移除文件描述符的可写事件
  - `loop.close()`: 关闭事件循环(无法重新运行)
  - `loop.shutdown_asyncgens()`: 清理所有未完成的异步生成器
  - `loop.shutdown_default_executor()`: 关闭默认线程池
- `Task`​类: future的子类, task将协程包装为一个调度单元, 在loop上实际运行

  - 用`asyncio.create_task()`​或`asyncio.gather()`​提交任务时, 实际会生成一个`Task`​对象
  - 协程本身只是逻辑代码, 一个可迭代对象, 不具有直接调度能力
- `async`​关键字: 声明异步上下文

  - `async + def`: 定义协程函数, 返回协程对象, 是用户级别的多任务, 可读性强, 不依赖回调
  - `async with`: 定义异步上下文管理器, 需实现`__aenter__`​和`__aexit()__`​方法

    - 常用`async with lock`​, enter时lock.acquire(), exit时lock.release()
  - `async for item in async_iterable`: 定义异步循环

  - `await`: 遇到await即挂起当前协程, 等待`await`​之后的协程完成后继续执行

    - await之后的必须是协程对象或支持await的异步对象
    - 只能在`async`​定义的异步上下文中使用, 表示异步操作的暂停点
  - `yield`: 暂停函数, 并产出一个数据
- 顶层API:

  - `asyncio.get_event_loop()`: 获取当前线程的事件循环, 如果没有事件循环, 则会抛出错误
  - `asyncio.new_event_loop()`: 创建一个新的事件循环
  - `asyncio.set_event_loop(loop)`: 设置当前线程的事件循环为指定的 `loop`​
  - `asyncio.get_running_loop()`: 获取当前运行中的事件循环(仅在协程或任务中调用时有效)

  - `asyncio.run(coroutine)`: 推荐, 创建事件循环、执行协程并在协程完成后关闭事件循环, 不需手动获取、启动和关闭
  - `asyncio.create_task(coroutine)`: 将协程包装为任务并提交到事件循环中, 适合子任务管理, 与当前上下文绑定, 不需直接操作事件循环, 更简洁
  - `asyncio.gather(*coroutines)`: 并发运行多个协程, 等待它们全部完成并返回`future`​对象
  - `asyncio.sleep(seconds)`: 异步延迟指定秒数, 不阻塞事件循环

  - `asyncio.Lock()`: 异步锁, 确保协程之间的同步

    ```python
    import asyncio
    
    lock = asyncio.Lock()
    
    async def critical_section(name):
        async with lock:
            print(f"{name} acquired lock")
            await asyncio.sleep(1)
            print(f"{name} released lock")
    
    async def main():
        await asyncio.gather(
            critical_section("Task 1"),
            critical_section("Task 2"),
        )
    
    asyncio.run(main())
    ```
  - `asyncio.Queue(maxsize=0)`: 异步队列, 用于在协程间传递数据
  - `asyncio.Event()`: 异步事件, 用于在协程之间进行事件触发

  - `asyncio.wait_for(coroutine, timeout)`: 在指定时间内等待协程完成, 超时抛出`TimeoutError`​
  - `asyncio.timeout(timeout)`: 上下文管理器形式的超时工具
  - `asyncio.shield(coroutine)`: 防止协程被取消

  ```python
  import asyncio
  
  def num_generator(m, n):
      """指定范围的数字生成器"""
      yield from range(m, n + 1)
  
  async def prime_filter(m, n):
      """素数过滤器"""
      primes = []
      for i in num_generator(m, n):
          flag = True
          for j in range(2, int(i ** 0.5 + 1)):
              if i % j == 0:
                  flag = False
                  break
                  if flag:
                      print('Prime =>', i)
                      primes.append(i)
                      await asyncio.sleep(0.001)
                      return tuple(primes)
                  
  async def square_mapper(m, n):
      """平方映射器"""
      squares = []
      for i in num_generator(m, n):
          print('Square =>', i * i)
          squares.append(i * i)   
        	await asyncio.sleep(0.001)
    	return squares                
  def main():
      """主函数"""
      loop = asyncio.get_event_loop()
      future = asyncio.gather(prime_filter(2, 100), square_mapper(1, 100))
      future.add_done_callback(lambda x: print(x.result()))
      loop.run_until_complete(future)
      loop.close()
  
  
      if __name__ == '__main__':
          main()                             

## `aiohttp`模块

异步HTTP客户端和服务器框架, 基于异步I/O构建, 可与asyncio模块一起工作, 常用于高并发场景, 如爬虫, API请求

### **客户端部分**

用于发起HTTP请求和处理响应, 核心类`aiohttp.ClientSession`

- HTTP方法: 用于发起不同类型的HTTP请求

  | **方法**               | **描述**                              |
  | ---------------------- | ------------------------------------- |
  | `session.get(url)`     | 发起 GET 请求                         |
  | `session.post(url)`    | 发起 POST 请求, 支持 `data` 或 `json` |
  | `session.put(url)`     | 发起 PUT 请求                         |
  | `session.delete(url)`  | 发起 DELETE 请求                      |
  | `session.head(url)`    | 发起 HEAD 请求, 只获取响应头部        |
  | `session.options(url)` | 发起 OPTIONS 请求                     |

  ```python
import aiohttp

async with aiohttp.ClientSession() as session:
    # GET 请求
    async with session.get('https://example.com') as response:
        print(await response.text())

        # POST 请求
        async with session.post('https://example.com', data={'key': 'value'}) as response:
            print(await response.json())
  ```

  常用参数:

  - `params`: 在URL中附加查询参数

    ```python
    async with session.get('https://example.com', params={'key': 'value'}) as response:
        print(await response.text())
    ```
  - `headers`: 设置自定义请求头

    ```python
    async with session.get('https://example.com', headers={'Authorization': 'Bearer token'}) as response:
        print(await response.text())
    ```
  - `timeout`: 设置请求超时时间

    ```python
    from aiohttp import ClientTimeout
    timeout = ClientTimeout(total=10)  # 超时时间 10 秒
    async with aiohttp.ClientSession(timeout=timeout) as session:
        async with session.get('https://example.com') as response:
            print(await response.text())
    ```
- 响应对象常用方法:

  |**方法**|**描述**|
  | ------| ----------------------------------|
  |​`response.text()`​|获取响应内容, 解码为字符串|
  |​`response.json()`​|解析响应内容为JSON格式(如果支持)|
  |​`response.read()`​|以字节流方式读取响应内容|
  |​`response.status`​|获取HTTP状态码(如200, 404等)|
  |​`response.headers`​|获取响应头, 返回字典类型|

  ```python
  async with session.get('https://example.com') as response:
      print(response.status)       # 状态码
      print(response.headers)      # 响应头
      print(await response.text()) # 响应内容
  ```
- 文件上传和下载方法:

  - 上传文件:

    ```python
    async with aiohttp.ClientSession() as session:
        with open('file.txt', 'rb') as f:
            async with session.post('https://example.com/upload', data={'file': f}) as response:
                print(await response.text())
    ```
  - 下载文件:

    ```python
    async with aiohttp.ClientSession() as session:
        async with session.get('https://example.com/file') as response:
            with open('file.txt', 'wb') as f:
                f.write(await response.read())
    ```

### **服务器部分**

用于构建异步HTTP服务, 核心类`aiohttp.web.Application`​

- 创建一个简单的HTTP服务器:

  ```python
  from aiohttp import web
  
  async def handle(request):
      return web.Response(text="Hello, World!")
  
  app = web.Application()
  app.router.add_get('/', handle)
  
  web.run_app(app, port=8080)
  ```
- 路由: 通过`app.router`​添加路由规则

  ```python
  app.router.add_get('/', handle)       # GET 请求
  app.router.add_post('/submit', handle) # POST 请求
  app.router.add_static('/static', '/path/to/static/files') # 静态文件
  ```
- JSON响应:

  ```python
  async def handle(request):
      return web.json_response({'message': 'Hello, World!'})
  ```

### **其他功能**

- Session管理: 支持基于Cookie的会话管理, 提供`aiohttp.ClientSession`​生命周期管理
- 代理支持: 支持通过代理服务器发起请求

  ```python
  async with aiohttp.ClientSession() as session:
      async with session.get('https://example.com', proxy='http://proxy.com') as response:
          print(await response.text())
  ```
- 连接池: `aiohttp`​默认实现了连接池, 减少了重复连接开销
- WebSocket支持:

  ```python
  async with session.ws_connect('wss://example.com/socket') as ws:
  	await ws.send_str('Hello, WebSocket!')
  	msg = await ws.receive()
  	print(msg.data)
  ```

```python
import asyncio
import re

import aiohttp

PATTERN = re.compile(r'\<title\>(?P<title>.*)\<\/title\>')


async def fetch_page(session, url):
    async with session.get(url, ssl=False) as resp:
        return await resp.text()


async def show_title(url):
    async with aiohttp.ClientSession() as session:
        html = await fetch_page(session, url)
        print(PATTERN.search(html).group('title'))


def main():
    urls = ('https://www.python.org/',
            'https://git-scm.com/',
            'https://www.jd.com/',
            'https://www.taobao.com/',
            'https://www.douban.com/')
    loop = asyncio.get_event_loop()
    cos = [show_title(url) for url in urls]
    loop.run_until_complete(asyncio.wait(cos))
    loop.close()


if __name__ == '__main__':
    main()
```

‍

‍
