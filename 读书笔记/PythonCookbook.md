# PythonCookbook

# 数据结构和算法

## 解包赋值

任意序列/可迭代对象，可通过赋值语句解压并赋值给多个变量

前提：变量的数量必须跟序列元素的数量一样, 否则产生ValueError异常

```python
>>> data = [ 'ACME', 50, 91.1, (2012, 12, 21) ]
>>> name, shares, price, date = data
>>> name
'ACME'
>>> name, shares, price, (year, mon, day) = data
>>> year
2012
```

### 用`_`​或`ign`​占位不需要的变量

```python
>>> data = [ 'ACME', 50, 91.1, (2012, 12, 21) ]
>>> _, shares, price, _ = data
>>> shares
50
>>> price
91.1
```

### `*`​：扩展的迭代解压语法

适用于可迭代对象可变长时

```python
first, *middle, last = grades  	# middle必为列表类型

# 字符串使用
line = 'nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false'
uname, *fields, homedir, sh = line.split(':')

# 用于开头
*trailing_qtrs, current_qtr = sales_record 

record = ('ACME', 50, 123.45, (12, 18, 2012))
name,*_, (*_, year) = record

items = [1, 10, 7, 4, 5, 9]
head, *tail = items
```

## ​`deque`​类

​`append()`​、`appendleft()`​、`pop()`​、`popleft()`​

插入和删除的时间复杂度为`O(1)`​(列表为`O(n)`​)

用法：保留最后N个元素

## `heapq`​类

建立大根堆需传递负数

​`heapq.nlargest(n, nums[, key])`​、`heapq.nsmallest(n, nums[, key])`​：查找最大/最小的N个元素，当n与集合大小相近时排序后切片更快

​`heapq.heapify(nums)`​: 建立堆, 修改原数据(堆排序，不是严格升序，但第一个元素永远最小)

​`heapq.heappop(nums)`​: 弹出第一个元素，自动调整堆

​`heapq.heappush(nums, new_number)`​

### 实现优先级队列

```python
import heapq


class PriorityQueue:
	def __init__(self):
		self._queue = []
		self._index = 0
	
	def push(self, item, priority):
		heapq.heappush(self._queue, (-priority, self._index, item))
		# 负数优先级，index用于同优先级比较
		self._index += 1

	def pop(self):
		return heapq.heappop(self._queue)[-1]


# 应用
class Item:
	def __init__(self, name):
		self.name = name

	def __repr__(self):
		return 'Item({!r})'.format(self.name)


>>> q = PriorityQueue()
>>> q.push(Item('foo'), 1)
>>> q.push(Item('bar'), 5)
>>> q.push(Item('spam'), 4)
>>> q.push(Item('grok'), 1)
>>> q.pop()
Item('bar')
>>> q.pop()
Item('spam')
```

## ​`defaultdict`​类

一个键对应多个值：将多个值放到容器中

​`from collections improt defaultdict`​

构造：

- ​`d = defaultdict(list)`​

  - ​`d['a'].append()`​
- `d = defaultdict(set)`​

  - ​`d['a'].add()`​

特性：键不存在时，自动设置默认值

```python
# 原始实现
d = {}
for key, value in pairs:
	if key not in d:
		d[key] = []
	d[key].append(value)

# 使用defaultdict实现
d = defaultdict(list)
for key, value in pairs:
	d[key].append(value)
```

## ​`OrderedDict`​类

​`from collections import OrderedDict`​

迭代操作时会保持元素被插入的顺序，重复赋值不改变顺序

内部维护一个根据键插入顺序排序的双向列表，新元素会放到链表尾

大小是普通字典的两倍

## 字典运算

‍
