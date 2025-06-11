# 操作Excel

# 管理

- 导包: `import openpyxl`​

- 创建新工作簿对象: `wb = openpyxl.Workbook()`​, 注意 W 大写, 后面有圆括号
- 新建工作表: `ws1 = wb.create_sheet(title, index)`​, 可指定表名和位置
- 选中切换到要操作的工作表(必须先选中才能操作), 两种方式: 

  - ​`ws = wb['Sheet']`​
  - ​`ws = wb.active`​
- 加载现有的工作簿: `wb = openpyxl.load_workbook(filename)`​
- 保存工作簿: `wb.save(filename)`​

# 增

- 以单元格添加

  - ​`ws2.cell(6,6).value = '晴天'`​
  - ​`ws2.cell(行, 列, 数据)`​

    - 可使用循环添加数据

      ```python
      data = ['学号', '姓名', '年龄', '性别', '班级编号'] 
      for i in range(len(data)): 
      	ws.cell(1, i+1, titles[i])
      # 或者
      data = [( ), ( ), ( )……] 
      for row, data1 in enumerate(data, start=1): 
      	for col, ele in enumerate(data1, start=1): 
      		ws.cell(row, col, ele)
      ```
- 以行添加: `ws2.append(['编号',  '班级名称', ……])`​, 必须为序列形式, 默认在原有数据的下一行添加

> excel的行列是从1开始的, 区别于下标

# 查

- 获取所有工作表的名称: `print(wb.sheetnames)`​
- 获取单元格数据: `print(cell.value)`​

  - 使用循环获取所有数据

    ```python
    for row_data in ws.rows: 
    	print(row_data) 
    	for cell in row_data: 
    		print(cell.value, end='\t') 
    	print( )
    ```
- 总行数: `print(ws.max_row)`​
- 总列数: `print(ws.max_column)`​

# 改

- 修改工作表的名称: `ws.title = '新的名称'`​
- 合并excel

  ```python
  # 准备接受数据
  new_workbook = openpyxl.Workbook()
  new_sheet = new_workbook['Sheet']  

  # 获取每一张表
  for pos in range(len(data_workbook.sheetnames)): 
  	sheet_name = data_workbook.sheetnames[pos]  
  	sheet = data_workbook[sheet_name]

  # 合并数据
  rows = list(sheet.rows)  
  if pos != 0: 
  	rows = rows[1:]  # 非第一张表则去除表头 
  for row_data in rows: 
  	cell_datas = [cell.value for cell in row_data] 
  	new_sheet.append(cell_datas)

  # 保存
  new_workbook.save('./数据整合版.xlsx')
  ```
- 合并文件夹下各文件的数据

  ```python
  # 导包, 构造工作簿、工作表
  import os 
  import openpyxl 
  new_workbook = openpyxl.Workbook() 
  new_sheet = new_workbook['Sheet']

  # 获取路径
  dir_path = './各省数据' 
  filenames = os.listdir(dir_path)
  # 路径拼接
  for index in range(len(filenames)): 
  	fn = filenames[index]   
  	join_path = os.path.join(dir_path, fn) 
  	data_workbook = openpyxl.load_workbook(join_path)

  # 获取每一张工作表
  sheet_name = data_workbook.sheetnames[0]   
  data_sheet = data_workbook[sheet_name] 		# 此处保留了第一张表的名称

  # 遍历获取每一行
  rows = list(data_sheet.rows) 
  if index != 0:
  	rows = rows[1:] 
  for row_data in rows: 
  	cell_datas = [cell.value for cell in row_data] 
  	new_sheet.append(cell_datas)

  # 保存
  new_workbook.save('./合并.xlsx')
  ```
