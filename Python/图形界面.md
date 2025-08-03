# 概述

图形界面，Graphical User Interface, GUI, 图形用户界面, 由**窗口**和**控件**组成

![截屏2024-12-25 19.53.10](../assets/截屏2024-12-25%2019.53.10-20241225195316-z153pf0.png)​

- Panel面板: 一个没有标题栏的容器, 可容纳其他控件的控件
- StaticText静态文本

构建wxPython程序需要两个对象: 

- 一个应用对象: wx.App
- 一个窗口对象:wx.Frame

```python
import wx

app = wx.App()			 # 创建应用程序对象
frm = wx.Frame(None, title='', size=(), pos=()) 	# 创建窗口对象, 一参为所在父窗口

frm.Show() 		# 显示窗口, 默认隐藏
app.MainLoop() 	# 让应用程序进入主事件循环中
```

# 控件

```python
class MyFrame(wx.Frame):	# 自定义窗口类
	def __init__(self):
		super().__init__(None, title='第一个wxPython程序', size=(400, 300), pos=(100, 100))
		# 以下均为控件代码
		# 创建面板对象, parent=self即设面板所在父容器为当前窗口对象
		panel = wx.Panel(parent=self)
		# 创建静态文本对象, 并放到panel面板中, label为显示文字, pos设置文本位置
		statictext = wx.StaticText(parent=panel, label='Hello World!', pos=(10, 10))
```

继承自`wx.Control类`​

## 文本输入控件

- 普通文本输入控件 (只能输入单行文本): `tc1 = wx.TextCtrl(panel)`​
- 多行文本输入控件: `tc2 = wx.TextCtrl(panel, style=wx.TE_MULTILINE)`​
- 密码输入控件: `tc2 = wx.TextCtrl(panel, style=wx.TE_PASSWORD)`​

## 单选按钮、复选框

![截屏2024-12-25 20.58.30](../assets/截屏2024-12-25%2020.58.30-20241225205833-6805nv0.png)

- 单选控件: `wx.RadioButton`​, 同一组的多个单选按钮互斥, 一个按下, 其他必释放
- 多选控件: `wx.CheckBox`​, 可单独使用, 能提供两种状态的开和关

```python
class MyFrame(wx.Frame):
	def __init__(self):
		super().__init__(None, title='单选按钮和复选框', size=(400, 300))
		panel = wx.Panel(parent=self)

		st1 = wx.StaticText(panel, label='选择你最喜欢的编程语言: ')
		cb1 = wx.CheckBox(panel, id=1, label='Python') 	# 创建单选按钮
		cb2 = wx.CheckBox(panel, id=2, label='Java')	# 创建单选按钮
		cb2.SetValue(True) 		# cb2初始状态为选中
		cb3 = wx.CheckBox(panel, id=3, label='C++')		# 创建单选按钮
		# 绑定id为1～3的所有控件的事件处理到on_checkbox_click()方法
		self.Bind(wx.EVT_CHECKBOX, self.on_checkbox_click, id=1, id2=3)

		st2 = wx.StaticText(panel, label='选择性别: ')
		# 设置style=wx.RB_GROUP的按钮为一个组的开始, 直到遇到另一个设wx.RB_GROUP的按钮为止均为同一组
		radio1 = wx.RadioButton(panel, id=4, label='男', style=wx.RB_GROUP)  # 创建单选按钮
		radio2 = wx.RadioButton(panel, id=5, label='女')	# 创建单选按钮
		# 绑定id从4~5的控件到on_radio1_click()方法
		self.Bind(wx.EVT_RADIOBUTTON, self.on_radio1_click, id=4, id2=5)

		hbox1 = wx.BoxSizer()
		hbox1.Add(st1, flag=wx.LEFT|wx.RIGHT, border=5) 
		hbox1.Add(cb1)
		hbox1.Add(cb2)
		hbox1.Add(cb3)

		hbox2 = wx.BoxSizer()
		hbox2.Add(st2, flag=wx.LEFT|wx.RIGHT, border=5) 
		hbox2.Add(radio1)
		hbox2.Add(radio2)

		vbox = wx.BoxSizer(wx.VERTICAL)
		vbox.Add(hbox1, flag=wx.ALL, border=10) 
		vbox.Add(hbox2, flag=wx.ALL, border=10)

		#设置面板(panel)采用vbox布局管理器
		panel.SetSizer(vbox)
从事件对象中取出事件源对象(复选框)

	def on_checkbox_click(self, event):
		cb = event.GetEventObject()
		# 从事件对象中取出事件源对象(复选框)
		print('选择｛0｝, 状态｛1｝'.format(cb.GetLabel(), event.IsChecked())) # 获得复选框

	def on_radio1_click(self, event):
		rb = event.GetEventObject() 	# 事件对象中取出事件源对象(单选按钮)
		print('第一组｛0｝个被选中'.format(rb.GetLabel()))
```

## 列表控件

可单选/多选, `wx.ListBox类`​

![截屏2024-12-25 21.20.07](../assets/截屏2024-12-25%2021.20.07-20241225212010-43jv7l1.png)

style参数: 

- `wx.LB_SINGLE`: 单选
- `wx.LB_MULTIPLE`: 多选
- `wx.LB_EXTENDED`: 多选, 需要在按住Ctrl或Shift时选择项目
- `wx.LB_SORT`: 对列表选择项进行排序

```python
class MyFrame(wx.Frame):
	def __init__(self):
		super().__init__(None, title='列表', size=(400, 300))
		panel = wx.Panel(parent=self)

		st1 = wx.StaticText(panel, label='选择你喜欢的编程语言: ')
		list1 = ['Python', 'C++', 'Java']

		# 创建列表控件, choices设置列表选项, style设置列表风格
		lb1 = wx.ListBox(panel, choices=list1, style=wx.LB_SINGLE)
		self.Bind(wx.EVT_LISTBOX, self.on_listbox1, lb1)

		st2 = wX.StaticText(panel, label='选择你喜欢吃的水果: ')
		list2 =［'苹果', '橘子', '香蕉'］
		lb2 = wx.ListBox(panel, choices=list2, style=wx.LB_EXTENDED)
		self.Bind(wx.EVT_LISTBOX, self.on_listbox2, lb2)

		hbox1 = wx.BoxSizer()
		hbox1.Add(st1, proportion=1, flag=wx.LEFT|wx.RIGHT, border=5) 
		hbox1.Add(lb1, proportion=1)

		hbox2 = wx.BoxSizer()
		hbox2.Add(st2, proportion=1, flag=wx.LEFT|wx.RIGHT, border=5) 
		hbox2.Add(lb2, proportion=1)

		vbox = wx.BoxSizer(wx.VERTICAL)
		vbox.Add(hbox1, flag=wx.ALL|wx.EXPAND, border=5) 
		vbox.Add(hbox2, flag=wx.ALL|wx.EXPAND, border=5)

		panel.SetSizer(vbox)

	def on_listbox1(self, event):
		listbox = event.GetEventObject()
		print('选择{0}'.format(listbox.GetSelection())) # 返回单个选中项目的索引序号

	def on_listbox2(self, event):
		listbox = event.GetEventObject()
		print('选择{0}'.format(listbox.GetSelections())) # 返回多个选中项目的索引序号列表
```

## 静态图片控件

用于显示图片, `wx.StaticBitmap类`​

最后图片替换后, 需重写绘制窗口, 否则布局会混乱

![截屏2024-12-25 21.37.25](../assets/截屏2024-12-25%2021.37.25-20241225213728-vchzqxu.png)

```python
class MyFrame(wx.Frame):
	def __init__(self):
		super().__init__(None, title='静态图片控件', size=(300, 300))
		panel = wx.Panel(parent=self)

		# 创建wx.Bitmap图片对象的列表
		self.bmps = [wx.Bitmap('images/bird5.gif', wx.BITMAP_TYPE_GIF),
					 wx.Bitmap('images/bird4.gif', wx.BITMAP_TYPE_GIF),
					 wx.Bitmap('images/bird3.gif', wx.BITMAP_TYPE_GIF)]

		b1 = wx.Button(self.panel, id=1, label='Button1')
		b2 = wx.Button(self.panel, id=2, label='Button2')
		self.Bind(wx.EVT_BUTTON, self.on_click, id=1, id2=2)

		# 静态图片控件对象, self.bmps[0]是静态图片控件要显示的图片对象
		self.image = wx.StaticBitmap(self.panel, bitmap=self.bmps[0])

		#创建垂直方向的布局管理器对象vbox
		vbox = wx.BoxSizer(wx.VERTICAL)
		# 添加标控件到布局管理器对象vbox
		vbox.Add(b1, proportion=1, flag=wx.EXPAND) 
		vbox.Add(b2, proportion=1, flag=wx.EXPAND) 
		vbox.Add(self.image, proportion=3, flag=wx.EXPAND)

		self.panel.SetSizer(vbox)

	def on_click(self, event):
		event_id = event.GetId()
		if event_id == 1:
			self.image.SetBitmap(self.bmps[1]) # 重新设置图片, 实现图片切换
		else:
			self.image.SetBitmap(self.bmps[2])
		self.panel.Layout() 	# 重新设置panel面板布局
```

# 事件处理

![截屏2024-12-25 20.01.13](../assets/截屏2024-12-25%2020.01.13-20241225200115-9yz75ut.png)​

事件源: 事件发生的场所, 即各个控件, 如按钮事件的事件源就是按钮

事件: wxPython中的事件被封装为事件类`wx.Event`​及其子类

- 按钮事件类为`wx.CommandEvent`​
- 鼠标事件类为`wx.MoveEvent`​

事件处理程序: 一个响应用户事件的方法

```python
class MyFrame(wx.Frame):
	def __init__(self):
		super().__init__(None, title='事件处理', size=(400, 300))
		panel = wx.Panel(parent=self)
		self.statictext = wx.StaticText(parent=panel, label='请单击OK按钮', pos=(110, 20))

		# 创建按钮对象
		b = wx.Button(parent=panel, label='OK', pos=(100, 50))

		# 绑定事件, 一参为事件类型, 二参为事件处理程序, 三参为事件源
		self.Bind(wx.EVT_BUTTON, self.on_click, b)

	# 事件处理程序
	def on_click(self, event):
		self.statictext.SetLabelText('Hello World!')
```

# 布局管理

## 绝对布局

控件的位置和大小使用绝对数值, 控件不随窗口的变化而变化(尽量不用)

## 布局管理器

网格布局管理器和盒子布局管理器（推荐）

### 盒子布局管理器

`wx.BoxSizer类`​, 最常用, 可让子窗口/控件沿垂直/水平方向布局

![截屏2024-12-25 20.11.25](../assets/截屏2024-12-25%2020.11.25-20241225201127-fuv7k6y.png)

构造方法：

```python
wx.BoxSizer(wx.HORIZONTAL)		# 水平方向布局, 默认, 可省略
wx.BoxSizer(wx.VERTICAL)		# 垂直方向布局
```

`Add()`​方法: 添加子窗口/控件到父窗口

- `Add(window, proportion=0, flag=0, border=0)`: 添加到父窗口

    - proportion: 用于设置当前子窗口/控件在父窗口所占空间比例

    - flag: 布局标志, 用来控制对齐方式、边框和调整尺寸

        - 对齐标志

            ![截屏2024-12-25 20.18.36](../assets/截屏2024-12-25%2020.18.36-20241225201839-3kxaifk.png)

        - 边框标志

            ![截屏2024-12-25 20.19.03](../assets/截屏2024-12-25%2020.19.03-20241225201905-moc5x9t.png)

        - 调整尺寸标志

            ![截屏2024-12-25 20.19.29](../assets/截屏2024-12-25%2020.19.29-20241225201931-uf2ez33.png)​

    - border: 用于设置边框的宽度

- `Add(sizer, proportion=0, flag=0, border=0)`: 添加到另外一个布局对象, 用于布局嵌套

重构事件处理示例：

![截屏2024-12-25 20.29.57](../assets/截屏2024-12-25%2020.29.57-20241225203000-dfd8e47.png)

```python
class MyFrame(wx.Frame):
	def __init__(self):
		super().__init__(None, title='事件处理', size=(400, 300))
		panel = wx.Panel(parent=self)
		self.statictext = wx.StaticText(parent=panel, label='请单击OK按钮', pos=(110, 20))
		b = wx.Button(parent=panel, label='OK')
		self.Bind(wx.EVT_BUTTON, self.on_click, b)
	
		# 布局相关代码
		# 创建垂直方向的盒子布局管理器对象vbox
		vbox = wx.BoxSizer(wx.VERTICAL)

		# 添加静态文本到vbox布局管理器
		vbox.Add(
			self.statictext, 
			proportion=1, 		# 两个控件proportion都为1, 故各占一半
			flag=wx.ALIGN_CENTER_HORIZONTAL|wx.FIXED_MINSIZE|wx.TOP,  # 控件水平居中, 顶部有边框, 宽度为30
			border=30
		)

		# 添加按钮b到vbox布局管理器
		vbox.Add(b, proportion=1, flag=wx.EXPAND|wx.BOTTOM, border=10) 	# 完全填满

		# 设置面板panel采用vbox布局管理器
		panel.SetSizer(vbox)

	def on_click(self, event):	
		self.statictext.SetLabelText('Hello World!')
```

嵌套示例：

![截屏2024-12-25 20.55.06](../assets/截屏2024-12-25%2020.55.06-20241225205509-ybb2te4.png)

```python
class MyFrame(wx.Frame):
	def __init__(self):
		super().__init__(None, title='布局管理器嵌套', size=(300, 120))
		panel = wx.Panel(parent=self)
		self.statictext = wx.StaticText(parent=panel, label='请单击按钮')
		b1 = wx.Button(parent=panel, id=10, label='Button1')
		b2 = wx.Button(parent=panel, id=11, label='Button2')

		# 创建水平方向的盒子布局管理器hbox对象
		hbox = wx.BoxSizer(wx.HORIZONTAL)
		# 添加b1到hbox布局管理
		hbox.Add(b1, proportion=1, flag=wx.EXPAND|wx.ALL, border=10)
		# 添加b2到hbox布局管理
		hbox.Add(b2, proportion=1, flag=wx.EXPAND|wx.ALL, border=10)

		# 创建垂直方向的盒子布局管理器vbox对象
		vbox = wx.BoxSizer(wx.VERTICAL)
		# 添加静态文本到vbox布局管理器
		vbox.Add(self.statictext, proportion=1, flag=wx.CENTER|wx.FIXED_MINSIZE|wx.TOP, border=10)
		# 将水平hbox布局管理器对象嵌入到垂直vbox布局管理器对象
		vbox.Add(hbox, proportion=1, flag=wx.CENTER)

		# 设置面板panel采用vbox布局管理器
		panel.SetSizer(vbox)

		# 将两按钮b1、b2的单击事件绑定到self.on_click方法
		self.Bind(wx.EVT_BUTTON, self.on_click, id=10, id2=20)

	def on_click(self, event):
		event_id = event.GetId()	# 获得绑定按钮的id
		print(event_id)
		if event_id == 10:		# 根据id判断单击了哪个按钮
			self.statictext.SetLabelText('Button1单击')
		else:
			self.statictext.SetLabelText('Button2单击')
```