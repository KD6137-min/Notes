![image](../../assets//Image%20from%20Baidu-20250114111915-r9m728m.jpeg)

# Pandas

# Series

一维, 有序, 继承ndarray

- 创: `pd.Series()`​, data\=, index\=连续索引/离散索引, name\=列名称
- 查: 

  - 属性: 

    - `.shape`: 形状
    - `.size`: 长度
    - `.index`: 索引, 默认为np.arange(n)
    - `.values`: 值, 类型可为ndarray、list、dict、constants、tupple
    - `.name`: 索引名字
    - `.ndim`​
    - `.dtype`: 数据类型, 继承Numpy的所有类型，d表示data

      - `category`: 分类类型
      - datetime64: 时间类型, `pd.date_range()`​
      - `pd.api.types.is_*`​系列函数: 类型判断
      - NaN
    - `.head()`: 查看前几条数据, 默认5条
    - `.tail()`: 查看后几条数据, 默认5条
  - 索引: 

    - 显式索引: 使用索引名, 不可写下标, 包含结束值

      - `ser.index`​
      - `ser['index']`: <span data-type="text" style="font-family:">[]要加引号</span>
      - `ser.loc[' ']`: 一组[]索引, 得到值, Series类型
      - `ser.loc[[' ']]`: 两组[]索引, 得到值, 值本身数据类型, 可一次获取多个元素
    - 隐式索引: 使用下标(整数), 不可写索引名, 不包含结束值

      - `ser[]`​、​`ser.iloc[]`​、​`ser.iloc[[]]`​
  - 切片: 

    - 显式切片: <u>闭区间</u>, 必须写索引名, 不能为下标

      - `.loc[dim1切片, dim2切片, ...]`​
      - `[dim1切片, dim2切片, ...]`​
    - 隐式切片: <u>左闭右开</u>区间, 必须为下标, 不能为索引名

      - `.iloc[dim1切片, dim2切片, ...]`​
      - `[dim1切片, dim2切片, ...]`​
  - 判断: 

    - `.notnull()`: 判断是否不为空, 返回布尔值
    - `.isnull()`: 判断是否为空, 返回布尔值
  - `value_counts()`: 用于Series，统计元素出现次数(去重计数), 默认排除NaN，默认降序排列

    - 找出现次数最多的人：

      ```python
      # 第一种
      ans = orders.groupby('customer_number').size().reset_index(name='cnt')
      ans[ans['cnt'] == ans['cnt'].max()]['customer_number']
      
      # 第二种
      if len(orders)==0:
      	ans = pd.DataFrame({'customer_number':[]})
      else:
          ans = pd.DataFrame({'customer_number':[orders['customer_number'].value_counts().index[0]]})
      ```
    - 直接下属最多的：

      ```python
      man_cnt = employee['managerId'].value_counts()
      managers = man_cnt[man_cnt >= 5].index
      employee.loc[employee['id'].isin(managers),['name']]
      ```
- 改: 

  - 改索引名: `.index = ['A', 'B', 'C', 'D']`​ / `.index = dlist('ABCD')`​
  - 改值: 直接通过索引赋值
  - 运算: 适用Numpy的数组运算

    - 与Series: 运算中自动对齐索引, 不对应则补NaN, <u>无广播机制</u>

      - 加法: `+`​或`.add()`​, 保留所有index, fill\_value\=0
    - 成员运算符: `isin`​, 生成布尔序列
    - `between()`​
  - 排序: `.sort_values(key, axis, ascending, inplace, na_position)`​,

    - key: 排序依据的键, 可为单列、函数、列表
    - na_position: 缺失值的处理方式, 'first'/'last'

‍

---

‍

# Dataframe

数据框, 二维, 有序的列组成, 面向列

- 创:`pd.DataFrame()`​

  - data\=可传递字典创建, 字典的键为列索引, 字典的值(一个数组)为一列数据, 自动加上行索引, 若传入的列与键不匹配, 对应值为NaN

    - 若只有一个值，要用中括号变为向量：pd.DataFrame({'col': [value]})

  - index\=可指定索引顺序
  - columns\=列名

  - 转换: `.to_frame()`​
- 查:

  - 属性:

    - `.index`: 行索引, 设置index: df.index \= list('ABC')
    - `.columns`: 列索引

      - 设置列索引: df.columns \= ['name', 'age']
      - 只有列有name属性
    - `.values`: 值, 二维ndarray数组
    - `.shape`: 形状
    - `.style`​
    - `.dtypes()`: 查看数据类型, 继承Numpy的所有类型

      - `category`: 分类类型
      - 时间类型: 

        - datetime64: `pd.date_range()`​
        - 时间列: `.dt.*`​, 加dt后可以取到时间元素, 如year、month...
      - NaN
      - 类型判断: `pd.api.types.is_*_dtype`​系列函数
      - 类型转化:

        - `.astype()`​
        - `to_*`​系列函数
    - `.head()`: 查看前几条数据, 默认5条
    - `.tail()`: 查看后几条数据, 默认5条
    - `.info()`: 查看信息, memory\_usage\='deep'
    - `.describe()`: 查看列的描述性统计量, 默认只查看数值型列的

      - `include=`​

        - ['O']: 查看离散型列的统计信息
        - ['all']: 查看全部列的统计信息
      - `df.describe([0.01, 0.3, 0.4, 0.9, 0.99])`:  查看1%, 30%, 40%, 90%, 99%百分位数
      - 常用: `df.describe().round(2).T`​
    - accessor访问器

      - .dt: 

        - `.dt.date`​
        - `.dt.time`​
        - `.dt.normalize()`​
        - .dt.year/quarter/month/day /month\_name/weekday\_name /hour/minute/second/week/weekday /dayofweek/weekofyear/dayofyear
        - `.dt.is_leap_year`​
        - .dt.is\_month/year\_start/end
      - .cat: 

        - `.cat.codes`​
        - `.cat.categories`​
      - .str: 可使用str的全部方法

        - `.str[]`: 索引切片
        - `.str.len()`​
        - `.str.upper()`​
        - `.str.lower()`​
        - `.str.title()`: 每个单词的首字母大写, 其余小写
        - `.str.capitalize()`: 每个字符串的首字母大写, 其余小写
        - `.str.center(len, fillchar='')`: 用指定字符填充至指定长度后居中
        - `.str.ljust(len, fillchar='')`: 文本左对齐, 右侧填充至指定长度
        - `.str.rjust(len, fillchar='')`: 文本右对齐, 左侧填充至指定长度
        - `.str.pad(len, fillchar='')`: 等同于rjust
        - `.str.zfill(n)`: 用零在左侧填充至指定长度
        - `.str.slice()`: 切片, start\=, stop\=
        - `.str.repeat()`​
        - `.str.cat()`: 连接, sep\=''
        - `.str.find()`​
        - `.str.match()`​
        - `.str.extract()`: 可用正则
        - `.str.partition()`: expand\=False, 类似只分一次的split
        - `.str.contains()`: regex\=True使用正则
  - 索引: 

    - 对列索引: DataFrame默认先取列索引

      - 一个[], 返回Series类型: `df.语文`​、`df['语文']`​
      - 两个[], 返回DataFrame类型: `df.[['语文', '数学']]`​、`df.[['语文']]`​
    - 对行索引: 

      - 一个[], 返回Series类型: `df.loc['小明']`​、`df.iloc[0]`​
      - 两个[], 返回DataFrame类型: `df.loc[['小明', '小红']]`​、`df.iloc[[0, -1]]`​
    - 对元素索引: 

      - 先列后行: `df['语文']['小明']`​、`df['语文'][0]`​、`df.语文[0]`​、`df.语文.小明`​
      - 先行后列: 

        - `df.loc['小明']['语文']`​、`df.loc['小明'][0]`​、`df.loc['小明', '语文']`​
        - `df.iloc[0]['语文']`​、`df.iloc[0][0]`​、`df.iloc[0, 0]`: 一个[]相当于两个参数, 两个[], 里面的[]看作是一个参数
      - `.at[]`: 可混用索引, 直接返回元素的值, `.at[4, '数量']`​
    - 层次化索引:

      - 创建:

        - 隐式构造: 给index参数传递两个或更多的数组, 此方法同样适用于Series
        - 显式构造: `pd.Multiindex`​

          - `.from_arrays()`: 

            ```python
            index = pd.MultiIndex.from_arrays([
            	['1班', '1班', '1班', '2班', '2班', '2班'],
            	['张三', '李四', '王五', '鲁班', '张三丰', '张无忌']
            ], names=['班级', '姓名'])
            
            columns = pd.MultiIndex.from_arrays([
            	['期中', '期中', '期中', '期末', '期末', '期末'],
            	['语文', '数学', '英语', '语文', '数学', '英语']
            ], names=['考试阶段', '科目'])
            
            data = [...]
            
            df = pd.DataFrame(data, index=index,columns=columns)
            ```
          - `.from_tuples()`: 

            ```python
            index = pd.MultiIndex.from_tuples( 
            	( 
            		('1班', '张三'), 
            		('1班', '李四'), 
            		('1班', '王五'),  
            		('2班', '鲁班'), 
            		('2班', '张三丰'), 
            		('2班', '张无忌')
            	) 
            )
            ```
          - `.from_product()`: 

            ```python
            pd.MultiIndex.from_product(
            	[
            		['1班', '2班'],  
            		['张三',  '李四',  '王五'] 
            	] 
            )
            ```
      - 多层行索引:

        - df.loc['2班']
        - df.loc['2班'].loc['张三']]
        - df.loc['2班', '张三']
        - df.loc[('2班', '张三')]
        - df.iloc[1 : 5]
        - df.loc[('1班', '李四') : ('2班', '李四')]
        - df.loc['1班' : '2班']
        - df.iloc[1]
        - df.iloc[[1]]
        - df.iloc[[1, 3, 4, 2]]
      - 多层列索引: 

        - df[''][ ]…
        - df.col1.col2…
        - df.iloc[:, 2]
        - df.iloc[:, [0, 2, 4]]
        - df.loc[:, ('col1', 'col2')]
        - df.iloc[:, 1:5]
        - df.loc[:, '期中' : '期末']
      - 获取元素: `df[''][''][ ]…`​
      - 多层索引的堆叠: 

        - `.stack()`: 列转行，宽表变长表，将(默认为最里层的)列索引变成行索引
        - `unstack()`: 行转列，长表变宽表，将行索引变成列索引
        - 参数: level控制堆叠级别, fill\_value\=0
        - 索引层次从下标为0的最外层起算, 最内层为-1
  - 切片: 

    - 行切片: 

      - df[1:3]左闭右开
      - df.iloc[1:3]左闭右开
      - df['小明':'小黄']左闭右闭
      - df.loc['小明':'小黄']左闭右闭
    - 列切片: 

      - df.iloc[:, 1:4]
      - df.loc[:, '数学':'语文']
    - 同时切片: 

      - df.iloc[1:3, 1:4]
      - df.loc['小红':'小黄', '数学':'化学']
      - df.loc[['小红', '小蓝'], '数学':'化学']
  - 条件查询

    - `.query()`: 运算符\=\=\>\<and&or|\~、in/not in/isin([]), 使用变量时要加@

      - pandas中逻辑或应该用`|`​而不是or, or会导致ValueError
      - eg: n\=7  df.query('a\=\=@n')
    - `.filter()`​
    - `.select_dtypes()`: 筛选特定数据类型的列

      - include\=['int64', 'float6']
      - exclude\=[]
  - 抽样: `.sample()`: n\=随机查看n条数据, frac\=随机查看百分之多少的数据
  - 比较: `df1.compare(df2)`​

    - keep\_shape\=True: 保留数据框, 默认为False
    - keep\_equal\=True: 保留原始相同值 默认为False
- 改: 所有inplace=True均返回None，不用赋值给变量

  - 改索引:

    - `.reset_index(name='')`: 原行索引变为列, 行索引变为0, 1, 2, 3, …, name指定**数据列**的新列名
    - `.set_index(keys=['H5'])`: 将第五列的数据作为行索引
    - `.rename()`: 重命名索引

      - `{'张三':'Mr Zhang'}`: 键值对形式, 默认修改行索引名; axis\=1: 修改列索引名
      - index\={'张三':'Mr Zhang'}: 修改行索引名
      - columns\={'张三':'Mr Zhang'}: 修改列索引名
    - `df.columns = [' ', ' ', ' ']`: 直接改
    - `.rename_axis()`: 重命名索引的标签
  - 复制: `.copy()`​, 默认深拷贝
  - 转置: `.T`​
  - 透视: 

    - `pd.pivot(index='行索引', columns='列名', values='值列')`: 长表转宽表, 不涉及聚合计算, index和columns组合值唯一时可用, 有重复则抛出ValueError
    - `pd.pivot_table(data, values='值列', index='行索引', columns='列名', aggfunc='聚合函数', fill_value=None, margins=False)`: 聚合型行列转换, index+columns组合可重复, 需聚合统计, 后可接.query()、多层索引、多列聚合、总计、缺失值填充等

      - margins\=True: 最后追加一行总计, 默认为False
  - 逆透视: `pd.melt()`​

    - id\_vars\=主键列, 不转换
    - value_vars=[columns], 被melt的列名
    - var\_name\=melt列的新列名
    - value\_name\=原数据列的新列名
  - 运算: 

    - 与标量: `+`​、`-`​、`*`​、`/`​、`%`​、`//`​
    - 与Series: 

      - 使用Python操作符, 以行为单位操作, 参数必须是行, 对所有行生效, 可能出现NaN
      - 使用Pandas操作函数: 

        - axis\=0: 以列为单位操作(参数必须是列), 对所有列生效
        - axis\=1: 以行为单位操作(参数必须是行), 对所有行生效
    - 与DataFrame: 自动对齐不同索引的数据, 不对应则补NaN, 无广播机制

      - 除: `.divide()`​
      - 加: `+`​、`.add()`: df1.add(df2, fill\_value\=0): 先填充0, 再相加
  - 抽样: `.take()`​, 改变了行、列的顺序

    ```python
    df.take([1, 0, 2])              # 行排列 
    df.take([1, 0, 2], axis=1)	    # 列排列
    ```

    - 有放回抽样: `df.take(np.random.randint(0, 3, size=5))`​
    - 无放回抽样: `df.take(np.random.permutation([0, 1, 2]))`​
  - 排名: `.rank()`​, method='dense'/'first'
  - 替换: `.replace()`​, 作用于整个df, to\_replace\=待替换值, regex\=True使用正则, value\=

    - 灵活多列替换: to\_replace\={"列A":r"\\d","列B":"7"}, value\={"列A":1,"列B":"-"}
  - 合并: 

    - `pd.concat()`: pd.concat([df1, df2, df3]): 数据框类型要加[]

      - axis

        - \=0, 默认, 上下合并, 垂直合并

          - 列索引不匹配的, 补NaN
        - \=1, 左右合并, 水平合并

          - 行索引不匹配的, 补NaN
      - keys\=['x', 'y']: 新增索引来区分不同的表数据来源
      - ignore\_index\=重新生成索引, 默认为False
      - join\=默认'outer', 补NaN
      - 左连接: `.concat( ).reindex(左表.index)`​
    - `pd.merge()`:

      - 与concat的区别:

        - merge需要共同行/列
        - 自动根据相同column作为key来合并
        - 每一列元素的顺序不要求一致
        - 保留所有列索引和有相同列索引的行
      - how\=

        - 'inner': 内连接, 默认
        - 'outer': 全外连接, 缺失补NaN
        - 'left': 左连接
        - 'right': 右连接
        - 'cross'
      - on\=

        - 若key同名, 则on\=key, 可有多列, 若key不同名, 则left\_on\=,  right\_on\=
      - left\_index\=True,  right\_index\=True: 指<u>用行索引作为连接字段</u>![截屏2025-01-14 15.29.16](../../assets//截屏2025-01-14%2015.29.16-20250114152920-97liifg.png)

        ‍
      - suffixes\=['\_x', '\_y']: 同名字段不指定为连接字段时默认是同名不同义字段 自动添加后缀, 默认为\_x, \_y, 不建议使用, 可用add.prefix()或者rename(columns\={字段: 新字段})
    - `.join()`: 不匹配列, 只匹配行, 等同于left\_index\=True和right\_index\=True

      - 左表.join(右表): 合并, 并按照left的索引进行对齐
      - how\='outer'……
      - on\=指定列匹配合并
  - 聚合: 

    - `.agg()`: 

      - .agg([min, max, np.mean])
      - .agg({'金额': ['sum', 'count']}), 键值对形式, 均有引号, 不加括号
      - .agg(列名1\=('数据列', 'min'), 列名2\=('数据列', 'max'))
    - `.sum()`: 

      - axis\=0, 合为一行, 默认 axis\=1, 合为一列
      - level\=指定索引层数
    - `.max()`​、`.min()`​、`.median()`​、`.mean()`​
    - `.count()`: 非空值的数量
    - `cumsum()`: 累加、`cumprod()`: 累乘
    - `.std()`: 标准差
    - `.var()`: 方差
    - `.cov()`: 协方差, 两组数值中每对变量的偏差乘积的平均值

      - \>0: 两组变量正相关, \<0: 两组变量负相关, \=0: 两组变量不相关

      - df[0].cov(df[1]): 第0列和第1列的协方差
    - `.corr()`: 相关系数, \=X与Y的协方差/(X的标准差\*Y的标准差), 默认所有特征相关系数

      - 范围: [-1, 1], \>0为正相关, ＜0为负相关, \=0表示不相关, 绝对值越大, 相关程度越高

      - .corrwith(df[2]): 单一特征相关系数
    - `.idxmax(axis)`: 默认按列操作，找到最大值对应的第一个索引，axis=1找到每行中最大值对应的第一个索引
  - 排序: 

    - `.sort_index()`: 根据行索引排序, ascending默认True, 可为list(bool)或tupple(bool)
    - `.sort_values()`: 根据值排序, by\=[列1, 列2], ascending=[True, False], 可为list(bool)或tupple(bool)

      - axis\=1时, 将列排序
    - `.nlargest(n, 列)`: 按指定列拿前n名
  - 分组: 

    - `.groupby()`​, 分组之后不能使用swifter, by\=, as\_index\=默认True, 分组字段作为索引

      - .groups: 查看分组内容
      - .get\_group(): 查看指定组
      - .groupby().size().reset\_index(name\='数量'): 分组计数
      - 多聚合: `df = df.groupby('')[''].agg().reset_index()`​

        ```python
        df.groupby('列')
        .agg(
        	num_sold = ('product', 'nunique'),  
        	products = ('product', lambda x: ','.join(sorted(set(x)))) 
        )
        .reset_index()
        ```
      - ![截屏2025-03-01 23.04.21](../../assets//截屏2025-03-01%2023.04.21-20250301230429-nh62mt9.png)
  - 分箱: 

    - `pd.cut()`: 等距分箱

      - data\=
      - bins\=n: 分为n份; bins\=[0, 10, 20, 30]: 分箱断点; bins\=range()
      - right\=False: 左闭右开, 默认左开右闭
      - labels\=['a', 'b', 'c', 'd']: 分箱后的标签
    - `pd.qcut()`: 等频分箱, data\=, labels\=, q\=[0, 0.25, 0.5, 0.75, 1]: 百分比
  - 映射遍历: 

    - `.map()`: 常用在Series, 不能用于DataFrame, 可用lamda函数, na\_action\='ignore': 跳过缺失值
    - `.apply()`: 用于Series时x表示Series中元素, 用于DataFrame时x为Series, 可使用自定义函数
    - `.applymap()`: DataFrame专有方法, x为每个元素
    - `.transform()`: 

      - Series中使用, 可以执行多项计算

        - eg: df['数学'].transform([np.sqrt, np.exp])
        - eg: transform('mean')
      - DataFrame中使用, 默认处理每一列; axis\=1, 处理每一行
  - 计算列: 

    - `df[] = df.where(cond, other=NaN, axis=None, inplace=False, level=None, regex=False, method=None)`: 条件筛选/标记满足条件的元素

      - cond: 条件表达式
      - other: 若cond为False则替换为other
      - level: 若为多层索引, 可指定在哪个级别应用条件
      - regex: 若为True, 将cond视为正则表达式匹配
      - method: 指定填充的方式, 可结合pad和bfill
    - `df.assign(新列1=,  新列2=)`​
    - `df.eval()`: 表达式必须有引号

      - 无赋值: 计算结果, 不改变原数据, df.eval('列A+列B')
      - 有赋值: 新建计算列, 允许多个, df.eval('''列C\=列A+列B 列D\=列A-列B''')
      - inplace\=False: 是否改变原数据, 默认不改变
      - 结合f符使用: df.eval(f'占比\=数量/{数量sum}', inplace\=True)
    - `df['new'] = df['count'].diff()`: 默认减前一天count列
    - `df.rolling(window, min_periods=1, axis=0, closed=None).agg()`: rolling创建一个滚动窗口对象

      - window: 窗口大小, 可为固定行数或时间段, '3D'表示3天
      - min_periods: 计算结果所需的最小有效数据点数量, 默认1, 即窗口内至少有一个有效值时就返回结果
      - closed: 指定窗口是否包含窗口的结束位置, 可选'right'、'left'、'both'、'neither'

      ```python
      df = pd.DataFrame({
          'A': [1, 2, 3, 4, 5, 6, 7],
          'B': [7, 6, 5, 4, 3, 2, 1]
      })
      
      # 使用滚动窗口(大小为3)计算每个窗口的和和平均值
      result = df.rolling(3).agg({
          'A': 'sum',  # 对列 'A' 计算滚动窗口的和
          'B': 'mean'  # 对列 'B' 计算滚动窗口的平均值
      })
      
      # 使用多个聚合函数
      results = df.rolling(3).agg({
          'A': ['sum', 'mean', 'max'],
          'B': ['min', 'mean']
      })
      ```
    - `df.pct_change(periods=1, fill_method='pad', limit=None, freq=None, axis=0)`: 在相邻行之间计算百分比变化

      - periods: 要计算变化的周期, 默认1, 即计算当前行和上一行的变化
      - limit: 限制填充的次数, None表示无限制
      - freq: 用于处理时间序列数据
- 删: `.drop()`​, axis\=0默认删除行, index\='a'/['a','b']删除行, columns\='a'/['a','b']删除列

  - 条件删除: `df.drop(df[df['金牌数'] <20].index)`​
  - 删除列: `df.drop(df.columns[[7,8,9,10]], axis=1,inplace=True)`​
- 时间:

  - 创:

    - `pd.Timestamp('2020-2-1')`: 时刻数据, `pd.Timestamp('now')`​
    - `pd.Period('2020-2-1', freq=)`: 时期数据, freq\=Y年/M月/D日
    - `pd.bdata_range()`: 获取范围内所有工作日
    - 批量生成: periods\=n创建n个时间, freq\='D'以天为单位

      ```python
      pd.date_range('时间1', '时间2', periods=4, freq='D') 
      pd.period_range('时间1', '时间2', periods=4, freq='D')
      ```
  - 索引: `ts[pd.Timestamp('2020-02-02')]`​
  - 切片: `ts[pd.Timestamp(): pd.Timestamp()]`​

  - 转换:

    - `pd.to_datetime([ '2020.02.02',  '2020-02-02',  '2020/02/02'])`​

      - df['@timestamp'] \= pd.to\_datetime(df['@timestamp']).dt.floor('d')
      - `pd.to_datetime([时间],  unit='s')`: 时间戳-\>时间
    - `.astype(datetime64[ns])`​
  - 计算: 

    - dt +/- `pd.DateOffset()`: hours\=, days\=
    - dt +/- `pd.to_timedelta('1 day')`​

    - `(pd.Timestamp('now')-pd.to_datetime()).days`​
  - 索引: `ts['2030-03-15']`​、`ts['2030-03']`​、`ts['2030-3']`​、`ts['2030']`​
  - 切片: 

    - `ts['2020-02-02': '2020-03-03']`​
    - `ts[pd.date_range('2020-03-22', periods=10, freq='D')]`​
    - `df.set_index(时间列).truncate()`: before\=, after\=, axis\=
  - 属性: 

    - `.dt`​
    - `.index`​
    - `.index.year`​
    - `.index.month`​
    - `.index.day`​
    - `.index.dayofweek`​
  - 格式转换: `.strftime('%Y年%m月%d日-%H时%M分%S秒')`​
  - 时间移动: `.shift()`​, periods\=n: 后移n位, 默认后移一位, 后移空缺补NaN

    ```python
    import datetime 
    .shift(freq = datetime.timedelta(n))
    ```
  - 频率转换: 

    - `.asfreq()`​, fill\_value\=0, 结合`pd.tseries.offsets`​系列函数: 

      - `MonthEnd()`: 天-\>月, 对齐到月末
      - `Week()`: 天-\>星期
      - `Hour()`: 天-\>小时
  - 重采样: 时间列必须作为行索引, 即`df = 原表.set_index('日期')`​

    - `.resample()`​+聚合, on\=指定列进行汇总
    - 单位: 

      - 'AS': 以年为单位
      - 'Q': 以季度为单位
      - '2M': 以2月为单位汇总
      - 'BM': 采集月末
      - '2W': 以2周为单位汇总
      - '2D': 以2天为单位汇总
      - '2H': 以2小时为单位汇总
      - '2T': 以2分钟为单位汇总
      - 'S': 以秒为单位
    - `.resample().last()`: 保留每个单位最后一个数据
  - 时区tz: timezone

    - pytz.common\_timezones: 常用时区, 需import pytz

    - `.tz_localize(tz='UTC')`​
    - `.tz_convert(tz='')`: 时区转换
- 其他操作: 

  - `display()`: 原样输出, 不同于print
  - 哑变量: 用于数据脱敏、节约内存

    - `astype('category').cat.codes()`: 返回Series, 每个类别赋予唯一整数编码
    - `.get_dummies()`: 独热编码，将分类变量转为多个二进制列，每列表示一个类别是否存在，`drop_first=True`​只保留n-1个独热列，避免多重共线性问题

      - dummy variable：统计学术语，虚拟变量，将分类变量转为数值变量的方法
      - `dummy_na=True`: 将NaN视为一个类别
    - `pd.factorize()`: 因子化, 返回元组, 第一个元素为包含所有整数编码的数组, 第二个元素为Index对象, 包含唯一类别
  - 爆炸列: 只有DataFrame有

    - `.str.split().to_frame().explode()`​
    - `df.explode('列')`​
    - `df.explode(list(''))`​
  - swifter: 加快运行速度, 不能用在groupby后面

# Panel

带标签的三维数据, 有序的DataFrame(不常用)

# ETL

- E: extract数据提取

  - pd.read\_\*系列函数
  - csv

    - pd.read\_csv( )

      - path: 路径
      - delimiter: 分隔符
      - header: 有无字段名称
      - usecols\=[ 0, 1, 3, ……]/['列名', '列名', '列名']

        - 使用哪列
      - names: 列名(没有字段名使用)
      - engine: 数据计算引擎('c'|'python')
      - skiprows\=

        - lambda x: ……
      - index\_col\=

        - 指定列作为索引
      - nrows: 获取多少行
      - encoding: 加载数据的编码

        - 含中文时, 'gbk'
      - encoding\_errors: ('strict'|'ignore')
      - data\_format
      - keep\_default\_na

        - 默认为True, 将缺失值标记为NA
      - na\_values\=

        - 将自定义值视为缺失值
      - na\_rep\=

        - 将缺失值标记为指定值
      - na\_filter\=

        - 默认为True, 处理缺失值 \=False, 不处理缺失值
      - dtype\=

        - {'a': str, 'b': str}
        - 更改指定列的类型
      - parse\_dates\=['时间列']

        - 时间列设置为时间格式
      - chunksize\=n

        - 返回可迭代对象, 每次读取n行
    - pd.read\_table( )

      - sep默认\='\\t', 需要修改为','
    - 分批获取

      - total \= 0 skip \= 1 nums \= 10000 while True: try :  df \= pd.read\_csv("xxx.csv", header\=None, skiprows\=skip, nrows\=nums, usecols\=ind, names\=["金额"]) if df.shape[0] \=\= 0: break df["金额"] \= df["金额"].astype("float64") total +\= df["金额"].sum() skip +\= nums except Exception as e: break
  - Excel

    - df \= pd.read\_excel( )

      - path: 路径
      - sheet\_name: 读取工作表(下标/表名)
      - delimiter: 分隔符
      - header: 有无字段名
      - names: 列名(无字段名时使用)
      - encoding: 加载数据的编码
      - encoding\_errors: ('strict'|'ignore')
    - 数据合并

      - 同一工作簿多表合并

        - 上面sheet\_name拿到多表名 pd.concat(list(df.values( )))
      - 同一目录下多簿合并

        - 使用glob包

          - from glob import glob
          - 只拿文件名, 不加载文件
        - filenames \= glob('路径/\*.xlsx')
        - dfs \= [ ] for file in filenames: dfs.append(pd.read\_excel(file)) pd.concat(dfs)

          - 或者dfs \= [pd.read\_excel(f) for f in filenames]
  - 剪切板

    - pd.read\_clipboard( )
  - 数据库

    - 使用sqlalchemy包

      - from sqlalchemy import create\_engine
    - con \= create\_engine( 'mysql+pymysql:// 用户名: 密码@host: 端口号/ 库名?charset\=utf8')
    - pd.read\_sql( sql\='select \* from 表名' con\=con)
  - read\_json( )
  - read\_hdf(file, 'table')
  - read\_html(url)
- T: transform数据预处理

  - 数据集成: 把多来源数据合并至同一位置, 同一客体统一命名
  - 数据规约: 压缩降维数据, 但保持数据完整性
  - 数据清洗: 无关数据、重复值、空值、异常值/离群点、冗余属性、平滑噪声数据

    - 缺失值处理

      - 空值类型

        - None: Python自带的空值对象, 不参与任何计算
        - NaN: not a number
        - NaT: not a time
      - 判断函数

        - 参数axis全部默认\=0, 找空列; axis\=1时, 找空行
        - isna( )和isnull( )

          - isna( )适用于所有数据类型, isnull( )只适用于numpy数组
          - 本质相同, 建议使用isna( )
        - .isnull( )

          - isnull( ).sum( )

            - 查看空缺值数量
          - isnull( ).mean( )

            - 查看空缺值占比
          - .isnull( ).all( )

            - 全部为True才为True
          - .isnull( ).any( )

            - 存在True即为True
            - 行过滤

              - cond \= df.isnull( ).any(axis\=1) df[\~cond]
            - 列过滤

              - cond \= df.isnull( ).any( ) df.loc[:, \~cond]
        - .notnull( )

          - .notnull( ).all( )

            - 行过滤

              - cond \= df.notnull( ).all(axis\=1) df[cond]
            - 列过滤

              - cond \= df.notnull( ).all( ) df.loc[:, cond]
          - .notnull( ).any( )
      - 处理方法

        - 删除

          - dropna( )

            - axis\=0, 默认, 删除有空的行 axis\=1, 删除有空的列
            - subset\=['列1',  '列2', ……]

              - 指定列进行空值处理
            - how\=

              - 'any': 默认, 删除存在空的行
              - 'all': 删除全部为空的行
            - ignore\_index\=True

              - 删除后重新形成新的连贯索引
            - inplace\=True: 修改原数据, 返回None
        - 填充

          - fillna( )

            - 固定值填充

              - value\=

                - 均值、中位数、众数 参考值、标准值、特殊值……

                  - df['评价人数'] \= df['评价人数'].fillna(df['评价人数'].mean())
                  - 上下的均值

                    - fillna(df[''].interpolate( ))
            - 就近填充

              - method\=

                - \='ffill': 向前填充, 即复制上面的数据

                  - \='ffill', axis\=1: 向左填充, 即复制左边的数据
                - \='bfill': 向后填充, 即复制下面的数据

                  - \='bfill', axis\=1: 向右填充, 即复制右边的数据
              - .bfill( )
              - .ffill( )
            - 回归填充
            - 函数填充
            - inplace\=False

              - 默认不改变原数据
            - limit

              - 限制填充的次数
        - 不处理
    - 重复值处理

      - Series

        - .is\_unique
        - .unique( )

          - 去重
        - .nunique( )

          - 去重计数
        - .duplicated( )

          - 检验重复的行, 返回布尔值
          - subset\=
          - keep\=
      - DataFrame

        - .drop\_duplicates( )

          - 删除重复的行

            - 注意接收
          - keep\=

            - 'first': 保留重复的第一行
            - 'last': 保留重复的最后一行
            - False: 标记所有重复行, 不保留
          - subset\=['col1', 'col2', 'col3']

            - 指定列进行检测
            - 若不写子集, 不删除任何一行
          - ignore\_index\=
          - inplace\=
        - .duplicated( )

          - 检验重复的行, 返回布尔值
          - subset\=
          - keep\=
        - .nunique( )，去重计数，默认按列，axis\=1按行

          ```python
          daily_sales.groupby(['date_id', 'make_name']).agg({
          	'lead_id':'nunique',
              'partner_id':'nunique',
          }).reset_index()
          # 等价于
          daily_sales
          .groupby(['date_id','make_name'])[['lead_id','partner_id']]
          .nunique().reset_index()
          ```
  - 数据变换

    - 数据抽取
    - 数据计算
    - 数据分组
    - 数据转换
- L: load数据加载

  - CSV

    - .to\_csv( )

      - file

        - hdfs://ip:port/demo.csv

          - 保存到hdfs
      - sep: 默认为', '
      - columns\=

        - 指定列保存
      - header: 是否保留列索引
      - index: 是否保留行索引
      - encoding\=

        - 'utf\_8\_sig'
  - zip

    - compression\_opts \= dict(method\='zip', archive\_name\=)
    - .to\_csv('xx.zip', compression\=compression\_opts)
  - Excel

    - .to\_excel( )

      - sheet\_name: 工作表名
      - header\= n/list: 是否保留列索引/多层索引
      - index: 是否保留行索引
      - names\=list: 替换原来的列名
  - 数据库

    - from sqlalchemy import create\_engine
    - con \= create\_engine( 'mysql+pymysql:// 用户名: 密码@地址: 端口号/ 库名?charset\=utf8')
    - .to\_sql( )

      - name\='表名': 数据库中表名
      - con\=con: 数据库连接对象
      - index\=False: 是否保存行索引
      - if\_exists\=

        - 'append': 追加写
        - 'fail': 新建
        - 'replace': 完全替换
  - markdown

    - .to\_markdown( )
  - html

    - .to\_html( )

      - file\=
      - col\_space\=

        - 列宽
      - border\=
      - justify\=

        - 'center': 居中
- 完整流程

  - 导包
  - 熟悉表结构display(\*.columns)
  - 合并数据

    - pd.merge( )
  - 空值处理

    - 空值检测

      - .isnull( ).sum( )
    - 查看空值

      - df.loc[df[ ].isnull( )]
      - df[df.isnull( ).T.any( ) \=\= True] .style.highlight\_null(null\_color\='')

        - 查看所有有空值的行
    - 去除冗余列

      - .drop('', axis\=1, inplace\=True)
    - 规范列名

      - .rename(col, inplace\=True)
    - 填补有规律值

      - .loc[ ] \=
  - 数据汇总

    - .query( ).groupby(by\=).sum( ).unstack(level\=-1)
  - 可视化

    - .plot(kind\='bar')

# CURD

- create

  - 插入列

    - df['新列']\=df.单价 \* df.数量
  - 插入行

    - 插入的数据 \= pd.DataFrame(pd.Series(x.iloc[ ].to\_dict( ))).T pd.concat([原表, 插入的数据], ignore\_index\=True)
    - df \= pd.concat([df, pd.DataFrame.from\_records([{ 'a': 1, 'b': 2 }])])
    - 在中间插入

      - 将原数据从插入位置分解, 再三者合并
      - df1 \= df.iloc[:n, :] df2 \= df.iloc[n:, :] df3 \= pd.DataFrame( ) df\_new \= pd.concat([df1, df3, df2], ignore\_index\=True)
- read

  - .query( )

    - expr\=

      - 只需一对引号包含, 里面的内容不用引号
- update

  - 赋值符\=修改
  - 注意改不改原表, 用不用变量接收
- delete

  - .drop(labels\=, axis\=, inplace-\=True)
  - del df['列']

# 显示设置

- 不改变数据, 仅优化显示
- 基于option的全局配置

  - pd.set\_option( )

    - 显示全部行/列, 不折叠

      - pd.set\_option('display.max\_rows')
      - pd.set\_option('display.max\_columns')
    - 显示指定行/列

      - pd.set\_option('display.max\_rows', m)

        - 预览只显示m行
      - pd.set\_option('display.max\_columns', n)

        - 预览只显示n列
    - 修改每列最大字符宽度(超出则显示…)

      - pd.set\_option ('display.max\_colwidth',10)
    - 修改默认显示精度为小数点后n位

      - pd.set\_option('display.precision', n)
    - 忽略警告

      - pd.set\_option("mode.chained\_assignment", None)
      - import warnings

        - warnings.filterwarnings('ignore')
    - 设置数值显示条件

      - pd.set\_option('chop\_threshold', m) : 若小于m则显示为0
    - 让 pandas 支持 LaTex

      - pd.set\_option("display.html.use\_mathjax",True)
    - 修改默认绘图引擎

      - pd.set\_option("plotting.backend","plotly")
    - 还原设置

      - 还原所有 option 设置

        - pd.reset\_option("all")
      - 还原所有显示设置

        - pd.reset\_option("\^display")
      - 还原显示数默认设置

        - pd.reset\_option("max\_rows")
        - pd.reset\_option("max\_columns")
- 基于style的个性配置

  - data.style为Styler对象, 将样式传递给DateFrame两种方法

    - Styler.applymap( )

      - 逐元素
    - Styler.apply( )

      - 逐列/行/表方式
      - axis\=0按列使用
      - axis\=1按行使用
      - axis\=None作用于整个表
  - 编写样式函数

    - eg: 负数显示红色字体 def color\_negative\_red(val): color \= 'red' if val \< 0 else 'black' return 'color: %s' %color

      - .applymap( )
    - eg: 每列最大值显示黄色背景 def highlight\_max(s): is\_max \= s \=\= s.max() return ['background-color: yellow' if v else '' for v in is\_max]

      - .apply( )
  - 调用样式函数

    - df.style.applymap(color\_negative\_red).apply( )……
    - subset\=

      - 指定列
    - 可混合调用
  - 修改属性

    - .style.set\_properties( )

      - subset\=['salary'],  \*\*{'color': 'red',  'background-color': '#F8F8FF', 'text-align':'center',       # 对齐方式 'font-size': '13px'}          # 字体大小
    - 可多个结合

      - .style .set\_properties( \*\*{'background-color': '#F8F8FF', 'text-align':'center',  'font-size': '13px'}) .set\_properties( subset\=['salary'], \*\*{'color': 'red'})
  - 空值设为红色

    - .highlight\_null(null\_color\='red')
  - 高亮数值列最大值

    - .style.highlight\_max( )
  - 高亮数值列最小值

    - .style.highlight\_min( )
  - 指定格式高亮

    - .style.highlight\_between(left\=3000, right\=10000)
  - 热力图

    - .style.background\_gradient(cmap\=)
    - 可自定义cmap

      - import seaborn as sns cm \= sns.light\_palette('green', as\_cmap\=True)
  - 条形图

    - .style.bar( )

      - color\=
      - subset\=
  - 旋风图

    - .style.bar( )

      - align\='mid': 以中点值为中心
      - color\=['', '']
  - 格式化输出

    - .style.format( )

      - '{:.2%}': 小数-\>百分数
      - '{'colA': '{: 0\<4.0f}', 'colB': '{:+.2f}'}'

        - 灵活对应
      - na\_rep\=''

        - 缺失值的标记
      - 日期类型的格式化

        - .style.format({'createTime': lambda t: t.strftime('%Y年%m月%d日')})
      - 指定列自定义格式化

        - .style .format("{0:,.2f}分", subset\="matchScore") .format("{""}元", subset\="salary")
  - 隐藏索引列

    - .style.hide\_index( )
  - 调整精度

    - .style.set\_precision(n): 将带有小数点的列精度调整为小数点后n位
  - 带样式导出

    - .style.……to\_excel/csv……

# EDA库

Exploratory Data Analysis，探索性数据分析，指通过统计方法和可视化技术对数据集进行初步分析，以理解数据分布、识别异常值、发现潜在模式或相关性，从而指导后续的模型设计和训练

核心目标：

- **数据理解**：分析特征分布（如均值、方差、偏度）、缺失值比例、类别平衡性等
- **异常检测**：通过箱线图、Z-score等方法识别离群值
- **特征关系**：利用热力图、散点图等分析特征间的相关性
- **数据质量**：检查重复数据、噪声或不一致性，为预处理（如归一化、填充缺失值）提供依据

在深度学习中的特殊作用：

- **数据预处理指导**：EDA可揭示是否需要标准化（如CNN对输入尺度敏感）或处理类别不平衡（如分类任务）
- **模型设计依据**：例如，发现数据存在长尾分布时，可能需采用加权损失函数或过采样技术
- **性能优化**：通过分析特征重要性，减少冗余特征以降低计算成本

典型方法：

- **统计摘要**：`df.describe()`​ 计算数值特征的基本统计量
- 可视化工具：

    - **直方图/密度图**：查看数据分布
    - **散点图/热力图**：分析特征间关系
    - **PCA/t-SNE**：降维后观察聚类现象
- **自动化工具**：如 `ydata-profiling`​ 可一键生成完整分析报告

EDA库：

- pandas\_profiling

  - import pandas\_profiling pandas\_profiling.ProfileReport( )
- sweetviz

  - import sweetviz as sv report \= sv.analyze( ) report.show\_html( )

# 可视化库

- Matplotlib
- from pivottablejs import pivot\_ui pivot\_ui(data)
- import pygwalker as pyw walker \= pyw.walk(data)
- from itables import init\_notebook\_mode, show init\_notebook\_mode(all\_interactive\=True) show(data)



# pandas和python的重要区别: 

在 Pandas 中使用 `apply()`​ 和直接在 DataFrame/Series 上进行逻辑操作时, 出现了 `and`​/`or`​ 与 `&`​/`|`​ 不同的原因, 主要与 **Python 的布尔运算** 和 **Pandas 的矢量化操作** 的设计模式有关。

### 1. Python 中的 `and`​/`or`​ 与 Pandas 中的 `&`​/`|`​ 的区别

- **​`and`​**​ **和** **​`or`​**​ 是 **Python 语言的关键字**, 用于对 **单个布尔值**(`True`​ 或 `False`​)进行运算。它们要求操作数是单个布尔值, 而不能是数组、Series 或 DataFrame。
- **​`&`​** ​ **和**  **​`|`​** ​ 是 **按位运算符**, 它们在 Pandas 中被 **重载**, 使其可以对 **Series 或 DataFrame** 进行按元素的逻辑运算(逐元素运算)。这种按元素的逻辑操作是 Pandas 中矢量化操作的一部分, 可以处理每个元素独立的布尔逻辑运算。

### 2. 在 `apply()`​ 中使用 `lambda`​ 和直接在 Pandas 中的差异

- **​`apply()`​** ​ **中的** **​`lambda`​**:
    在 `apply()`​ 中, `lambda`​ 通常处理的是单个元素(或者在 `axis=1`​ 时是每一行的数据)。由于 `lambda`​ 处理的是一个个元素(例如, 单个数值或一个布尔值), 因此可以直接使用 Python 中的 `and`​ 和 `or`​, 因为它们是为单个布尔值设计的。

    ```python
    df = pd.DataFrame({'a': [True, False, True]})
    df['b'] = df['a'].apply(lambda x: x and True)  # 使用 and
    ```

- **直接在 Pandas 中的操作**: 
    在 Pandas 中, 当你直接对 Series 或 DataFrame 进行操作时, 通常是在进行 **矢量化运算**, 也就是针对整个数组(Series 或 DataFrame)进行的逐元素操作。这时, `and`​ 和 `or`​ 就无法使用, 因为它们只能作用于 **单一的布尔值**, 而不是数组。所以在 Pandas 中, 必须使用 **按位运算符** `&`​ 和 `|`​ 来处理元素级的逻辑运算。

    ```python
    df = pd.DataFrame({'a': [True, False, True]})
    df['b'] = df['a'] & True  # 使用 & 来处理每个元素
    ```

### 3. 为什么会有这个差异？

- **​`and`​**​ **/**​**​`or`​**​ **的局限性**:
    ​`and`​ 和 `or`​ 是设计用来操作 **单个布尔值** 的, 在 Python 中, 它们不会逐元素地操作一个列表、Series 或 DataFrame。因此, Pandas 不允许在矢量化的 Series 或 DataFrame 上使用它们。
- **​`&`​** ​ **/**​ **​`|`​** ​ **的设计理念**:
    ​`&`​ 和 `|`​ 被设计为 **按位运算符**, 可以对多个布尔值进行逐元素运算。这符合 Pandas 在处理数据时的矢量化设计思想, 即通过并行处理每个元素来提高效率。

### 4. 在 Pandas 中正确使用 `&`​ 和 `|`​

当你在 Pandas 中进行布尔逻辑运算时, 必须使用  **​`&`​** ​(与)和  **​`|`​** ​(或)来进行逐元素的布尔运算。并且需要用括号将每个布尔条件括起来, 以确保运算顺序正确。

例如: 

```python
df = pd.DataFrame({'a': [True, False, True], 'b': [False, True, True]})

# 使用按位运算符进行逐元素布尔逻辑运算
df['c'] = (df['a'] & df['b'])  # & 用于按位与
df['d'] = (df['a'] | df['b'])  # | 用于按位或

print(df)
```

输出: 

```
       a      b      c      d
0   True  False  False   True
1  False   True  False   True
2   True   True   True   True
```

### 总结: 

- 在 **​`apply()`​** ​ 中使用 `lambda`​ 时, `and`​ 和 `or`​ 可以直接使用, 因为它们操作的是单个布尔值。
- 在 **Pandas** 中直接对 Series 或 DataFrame 进行逻辑运算时, 必须使用 **按位运算符** `&`​ 和 `|`​, 这是因为 Pandas 要对每个元素进行逐个计算, 而 `and`​ 和 `or`​ 只能操作单个布尔值。
- 记得在使用 `&`​ 和 `|`​ 时, 为了确保优先级正确, 需要使用圆括号将每个条件括起来。

希望这解释清楚了为什么在 `apply()`​ 中可以使用 `and`​ 和 `or`​, 而在 Pandas 中需要使用 `&`​ 和 `|`​！

‍

---

‍

# df.where()和series.where()

`where()`​ 是 Pandas 中的一个非常有用的函数, 通常用于条件筛选。它的作用是根据给定的条件来替换 DataFrame 或 Series 中的元素, 或者标记满足条件的元素。

### `DataFrame.where()`​ 和 `Series.where()`​ 的基本用法

#### 语法: 

```python
DataFrame.where(cond, other=nan, axis=None, inplace=False, level=None, regex=False, method=None)
```

- **​`cond`​**: 条件表达式, 布尔值(`True`​ 或 `False`​)或者布尔数组。
- **​`other`​**: 如果 `cond`​ 为 `False`​ 的元素, 替换成 `other`​。默认是 `NaN`​。
- **​`axis`​**: 如果条件是按列(`axis=0`​)还是按行(`axis=1`​)进行应用。默认是 `None`​, 即不影响。
- **​`inplace`​**: 是否直接在原 DataFrame 或 Series 上进行操作, 默认是 `False`​, 即返回一个新的对象。
- **​`level`​**: 如果是多层索引, 可以指定在哪个级别应用条件。
- **​`regex`​**: 如果为 `True`​, 则 `cond`​ 会被当作正则表达式来匹配。
- **​`method`​**: 可以指定填充的方式(通常与 `pad`​ 和 `bfill`​ 配合使用), 不过在普通条件替换中不常用。

#### 返回值: 

- 如果条件满足, 则保留原值；否则, 将原值替换为 `other`​(默认是 `NaN`​)。

### 使用 `where()`​ 进行条件筛选

#### 1. 条件筛选并替换元素

```python
import pandas as pd

# 创建一个 DataFrame
df = pd.DataFrame({
    'A': [1, 2, 3, 4, 5],
    'B': [10, 20, 30, 40, 50]
})

# 使用 where 替换不符合条件的值
result = df.where(df > 2, other=0)  # 如果大于 2, 保留原值, 否则替换为 0
print(result)
```

输出: 

```
   A   B
0  0  0
1  0  0
2  3  30
3  4  40
4  5  50
```

在上面的代码中, `df.where(df > 2, other=0)`​ 表示, 如果某个元素大于 2, 则保留该值, 否则将其替换为 0。

#### 2. 条件筛选并替换某一列的值

```python
# 假设我们想替换列 'A' 中小于 3 的值
df['A'] = df['A'].where(df['A'] >= 3, 0)
print(df)
```

输出: 

```
   A   B
0  0  10
1  0  20
2  3  30
3  4  40
4  5  50
```

#### 3. 条件筛选并保留原值(不替换)

```python
# 保留符合条件的原值, 替换不符合条件的元素为 NaN
result = df.where(df > 2)
print(result)
```

输出: 

```
     A     B
0  NaN   NaN
1  NaN   NaN
2  3.0  30.0
3  4.0  40.0
4  5.0  50.0
```

这里, 只有 `df > 2`​ 的位置上的元素被保留, 其余被替换为 `NaN`​。

### 4. `where()`​ 结合 `inplace=True`​

如果想在原 DataFrame 上进行操作, 可以使用 `inplace=True`​。

```python
df.where(df > 2, other=0, inplace=True)
print(df)
```

输出: 

```
   A   B
0  0  0
1  0  0
2  3  30
3  4  40
4  5  50
```

这会直接修改原始 `df`​

‍

---

# loc和链式索引

链式索引可能会有SettingWithCopyWarning的问题, 但这里只是读取数据, 可能不会有警告

效率方面，.loc是直接访问, 而链式索引会生成中间DataFrame, 可能更慢一些

语法上, .loc的写法更明确, 行和列同时指定, 而链式的是先筛选行, 再选列

代码可读性, .loc可能更好, 尤其是对于有经验的Pandas用户来说, 更推荐这种方式

### **1.**  **​`students.loc[students['student_id'] == 101, ['name', 'age']]`​** ​

- **原理**:使用 `.loc`​ 一次性完成「布尔索引 + 列选择」, 是 Pandas 推荐的优化方式
- **特点**: 

    - ✅ **单步操作**: 直接通过标签索引定位数据, 无中间临时 DataFrame 生成
    - ✅ **高效**: 对大型数据集更高效
    - ✅ **语法明确**: 行列条件在同一 `[]`​ 中指定, 格式为 `[行条件, 列条件]`​

---

### **2.**  **​`students[students['student_id'] == 101][['name', 'age']]`​** ​

- **原理**: 
    通过「链式索引」分两步完成: 

    1. 先筛选行: `students[students['student_id'] == 101]`​
    2. 再选择列: `[['name', 'age']]`​
- **特点**: 

    - ⚠️ **链式操作**: 生成中间临时 DataFrame(第一步的结果)
    - ⚠️ **潜在性能问题**: 对大型数据集可能更慢
    - ⚠️ **可能触发警告**: 若尝试修改数据, 会触发 `SettingWithCopyWarning`​

---

### **关键区别总结**

| 特性               | `.loc`​ 方式              | 链式索引方式                     |
| ------------------ | ------------------------ | -------------------------------- |
| **执行步骤**       | 单步操作                 | 两步操作(生成中间临时 DataFrame) |
| **性能**           | 更高效(适合大数据量)     | 较低效(中间数据占用内存)         |
| **代码可读性**     | 更清晰(行列条件集中)     | 较分散                           |
| **修改数据安全性** | 安全(直接操作原数据视图) | 可能不安全(中间数据可能是副本)   |

---

### **示例对比**

#### 数据准备: 

```python
import pandas as pd

students = pd.DataFrame({
    'student_id': [101, 102, 103],
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [20, 21, 19]
})
```

#### 输出结果(两种方式相同): 

```python
# 输出: 
   name  age
0  Alice   20
```

### **扩展: **​ **​`.loc`​**​ **的其他优势**

```python
# 支持更复杂的条件(例如多列筛选)
students.loc[
    (students['age'] > 19) & (students['student_id'] != 102), 
    ['name', 'age']
]
```

# apply()

默认针对第一个维度，df默认针对列（若axis=1则针对行），series天然针对元素

`applymap()`：针对整个表

- **避免滥用 `apply()`**：优先使用 Pandas 内置的**向量化操作**（如 `df.sum()`、`df['A'] + df['B']`），效率远高于逐行/逐列的 `apply()`
- **元素级操作替代方案**：简单运算用向量化（如 `df * 2`），复杂逻辑可结合 `np.vectorize`或 `df.transform()`



```python
# 最小-最大归一化
df_normalized = (df - df.min()) / (df.max() - df.min())

# Z-score标准化
df_standardized = (df - df.mean()) / df.std()
```



