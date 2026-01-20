![image](../../assets//94af3258d32449e75a42f099ece0a8f0%201-20250114111731-bfse2wl.png)

# Numpy

# 概述

- 科学计算基础包, 可连接数据库
- 核心: `ndarray对象`​, 封装python原生数据类型n维数组, 默认<u>浮点数类型</u>, 维度称为轴
- ndarray与python数组(列表)区别:

  - numpy数组创建时具有固定大小(更改时创新删旧), python数组对象可动态增长
  - numpy数组要求元素数据类型相同, 在内存中大小相同, python数组允许元素大小不同

# 数据类型

- 整数:

  - `bit`​、`byte`​
  - `int8/uint8`: 1字节
  - `int16/uint16`: 2字节
  - `int/intc/int32/uint32`: 4字节
  - `intp/int64/uint64`: 8字节
- 布尔: 占1B, `True`​/`False`​
- 浮点: 存储方式: 符号位\*指数位\*尾数位, `float16`​、`float32`​、`float`​/`float64`​
- 复数: 用j代替数学中的i

  - `complex`​/`complex64`: 实部4字节+虚部4字节
  - `complex128`: 实部8字节+虚部8字节
- 时间:

  - `timedelta`: 时间跨度类型
  - `datetime64`: 时间戳类型, 8字节
- `object`: 字符、字符串、字典或其它特殊类型

  - 字符串`<Un`: `<`​表示小端字节序, `U`​表示Unicode字符串, `n`​表字符串的最大字符长度(字符数, 非字节数)

# 属性

- `.dtype`: 元素类型, 默认ndarray所有元素类型相同, 不同则统一为同一类型, 优先级: str\>float\>int

  - 类型转换

    - `.astype('float32')`​
    - `.tolist()`: 转成python列表
    - `.tofile()`: 以文本/二进制形式写入文件
- `.ndim`: 维度/轴数

  - 10: 0维数据/<u>标量</u>
  - [10]: 1维数据/<u>向量</u>
  - [[1, 2, 3], [4, 5, 6]]: 二维数据/<u>矩阵</u>
  - 二维以上: <u>张量</u>
- `.shape`: 查看形状, 返回<u>元组</u>, 列表没有shape
- `.size`: 总长度、总数据量
- `.itemsize`: 返回元素字节大小
- `.nbytes`: 返回总字节大小, 等于size\*itemsize
- `.strides`: 返回二元组, 单行字节大小和单元素字节大小
- `.real`: 复数数组的实部
- `.imag`: 复数数组的虚部

# 创建

- `np.ndarray(shape, dtype, buffer, offset, order, strides)`: 不推荐, 可能有未初始化的垃圾值, 不安全, 参数复杂, 优化内存时使用

  - buffer\=np.array([1, 2, 3]): 用于初始化数据
  - offset\=0: buffer中用于初始化数组的首个数据的偏移
  - order\='C'/F': C行优先, F列优先
  - strides: 每个轴的下标增加1时, 数据指针在内存中增加的字节数
- `np.array(object, dtype=None, copy=True)`: 可将列表、元组等可迭代对象转换为数组, 底层调用了np.ndarray(), 有初始化, 无垃圾值, 安全, 注意转换list时, 列表中元素类型不能混用, 如列表中有嵌套列表和普通元素, 要么同为普通元素, 要么同为嵌套列表

  - object: 传入一个可迭代对象, list、tupple、buffer(内存中的可迭代对象)
  - dtype: 数据类型
  - copy: 是否使用深拷贝
- routines函数: 

  - `np.empty(shape)`: 无需初始化
  - `np.zeros(shape)`: 返回全0矩阵
  - `np.ones(shape)`: 返回全1矩阵
  - `np.eye()`: 返回n阶单位矩阵, N行数, M列数, 默认为None, 表示和行一样, k \= 0向右偏移0个位置
  - `np.diag()`: 对角矩阵
  - `np.full(shape, fill_value)`: 返回fill\_value填充的矩阵
  - `np.arange()`: 返回指定间隔内均匀间隔值的一维numpy数组, start开始值(可选), stop结束值(不包含), step步长(可选)

    - `np.arange(n).reshape(x, y, z)`: 生成从0到n-1的一维数组, 再reshape
  - `np.linspace()`: 创建指定间隔内等差数列

    - start: 开始值
    - stop: 结束值
    - num\=50: 默认有50项
    - endpoint\=True: 是否包含结束值
    - restep\=False: 是否返回等差值(步长)
  - `np.logspace()`: 返回数以对数刻度均匀分布, start、stop、num(可选)

# 基本操作

- 读写: 

  - 保存: 

    - `np.save(file, arr)`:保存为'文件名.npy'
    - `np.savez(file, **args)`: file后缀为.npz, 数组以键值对形式保存
    - `np.savetxt(file, arr, delimiter=',')`: txt/csv文件
  - 读取: 

    - `np.load(file)`: 可读取某数组, 要设置变量接收, `np.load(file)['arr']`​
    - `np.loadtxt(file, delimiter=',')`: txt/csv文件
  - 图片: 需导包`import matpltlib.pyplot as plt`​

    - 读取: `plt.imread('./xxx.jpg')`​
    - 显示: `plt.imshow()`​
    - 图片类型为numpy.ndarray, 视频: 四维, 图片: 三维彩色, 二维黑白

      - 图片本质由数字组成, 三维数组RGB, 范围[0-255], R: red红色 G: green绿色, B: blue蓝色
- 索引: 

  - 一维下标访问: n[0], n[-1]
  - 列表类型索引: n[[3, 4, 5]], 可一次获取多个元素
  - 多维下标访问: n[3][4], 简写n[3, 4]
  - 通过索引修改数据: n[3, 4] \= 98, 可以直接改一行或一列等
  - 布尔索引: `x_prime = X[y == i, j]`​, X中y等于i的行中j列的值组成的数组
  - 混合索引: `array[row_indices, col_indices]`​

    - `y[[0, 1], [0, 2]]`​选择`[0, 0]`​、`[1, 2]`​两个元素
  - 升维索引：`array[None, :]` / `array[:, None]`，本质是用`np.newaxis`在当前位置插入一个长度为1的新轴，相比reshape插入位置更直观
- 切片: `n[行切, 列切, ...n维切]`​

  - 取一行: n[4]
  - 取连续多行: n[1:4]
  - 取不连续多行: 加中括号, `n[[1, 2, 4]]`​
  - 取一列: `n[:, 3]`​取第4列
  - 取连续多列: `n[:,2:5]`​
  - 取不连续多列: 加中括号`m[:, [1, 3, 5]]`​

  - 翻转: 

    - 行翻转: `n[::-1]`​
    - 列翻转: `n[:, ::-1]`​
    - 颜色翻转: RGB--\>BGR: `n[:, :, ::-1]`​
    - 交换行: `n[[1, 0, 2, 3]]`​
  - 马赛克操作: `th = 10`​, 设置马赛克宽度

    ```python
    for i in range(face.shape[0]//th): 
    	for j in range(face.shape[1]//th): 
    		face[i*th:(i+1)*th, j*th:(j+1)*th] = fece[i*th, j*th]
    ```
- 合并: 

  - `np.concatenate((n1, n2), axis=0)`: 参数为列表或元组, 级联的数组维度必须相同, axis参数改变级联方向

    - axis参数不写时, 默认为0(第一个维度), 即行级联, 上下合并; axis\=1, 列级联, 左右合并; axis\=2, 第三个维度拼接/颜色拼接
  - `np.hstack(n1, n2)`​/`np.colum_stack()`: 水平级联, 左右合并, 等同于axis\=1
  - `np.vstack(n1, n2)`​/`np.row_stack()`: 垂直级联, 上下合并, 等同于axis\=0
  - `np.dstack((a, b))`: 深度组合, 沿纵轴(深度)方向进行层叠加
  - `stack()`: 指定组合, 沿axis轴组合, 默认在最高维组合
- 拆分: 

  - `np.split(n, 2, axis=0)`: 对数组n从第三行(指定行)拆分, axis参数不写时, 默认为0, 上下拆分, 拆分后可索引:

    ```python
    cat = np.split(cat2, 2)
    cat[0]	# 上半部分
    cat[1]	# 下半部分
    ```
  - `np.hsplit(n, 2)`: 竖着分, 拆成柱, 平均分为2份, 可按照指定列拆分, 等同于split, axis\=1
  - `np.vsplit(n, 3)`: 横着分, 拆成条, 平均分为3份, 等同于split, axis\=0, 可按照指定行拆分

    - `np.vsplit(n, (1, 2, 4,))`​, 不包含索引所在的行, 即在行上面划分
    - 技巧: v开头即为axis=0, 水平操作, h开头即为axis=1, 垂直操作
  - `np.dsplit()`: 深度分割, n应为所有维度的公约数
  - `split(ary)`: 将数组拆分为多个子数组, 作为ary的视图
  - `array_split()`: 将数组拆分为多个子数组
- 拷贝: `np.copy()`​, 为深拷贝
- 排序: 默认升序, 降序加上[::-1]

  - `array.sort()`: 就地排序数组, 无返回值
  - `np.sort(array)`: 返回数组的排序副本, 不改变原数据
  - `np.lexsort(keys)`: 使用键的序列进行<u>间接稳定排序</u>, 返回排序后的索引, 可使用多键
  - `np.argsort()`: 返回对数组排序的索引
  - `msort()`: 返回沿第一个指针排序的数组的副本
  - `sort_complex()`: 先按实部排序, 再按虚部排序
  - `partition(arr, kth)`: 返回数组的分区副本
  - `argpartition(arr, kth)`: 使用kind关键字指定的算法, 沿给定的指针执行间接分区
- 空值处理: `.isnan()`​, 返回布尔值

# 维度操作

- `np.reshape(a, newshape, cmap)`​, 元素总量不变, 不改变原数组

  - newshape中-1表示自适应 列 \= -1, 则n行时有总size/n列 行 \= -1, 则n列时有总size/n行
  - cmap: 颜色参数
- `np.resize()`: 同reshape, 但直接修改原数组
- `np.ravel(a, order='C')`: 无论几维数据, 均压缩为一维数据，返回一连续的扁平化视图（若可能）或一个新数组（无法创建视图时）

  - a: 要展平的数组
  - order：指定展平的顺序，'C'：按行优先，'F'：按列优先，'A'：按输入数组的顺序，'K'：按元素在内存中的出现顺序
  - 因为返回视图，故而比`flatten()`​空间上高效，优先使用
- `.flatten()`: 从低维拼接展平，返回新的数组副本
- `.T/transpose()`: 转置
- 按轴操作: 

  - `np.moveaxis()`: 将数组的轴移到新位置
  - `np.rollaxis()`: 向后滚动指定的轴, 直到其位于给定的位置
  - `np.swapaxes()`: 互换数组的两个轴
  - `np.expand_dims()`: 扩展数组的形状
  - `.squeeze()`: 去除所有维度为1的维度，返回新数组，指定axis时只去除该维度（若该维度不为1则报错）
- 索引升维：`box[:,None,:]`，在指定位置增加一维

# random

- `.rand()`: [0-1)区间浮点数
- `.random( )`: [0, 1)区间浮点数, size数组形状, 元组形式, 默认只输出一个随机值
- `.randint()`: 随机整数, low最小值, high最大值, size数组形状, 元组, 默认只输出一个随机值

  - high \= None时, 生成数值在[0, low)区间
  - 若使用high值,  生成数值在[low, high)区间
- `.shuffle()`: 随机顺序排序
- `.choice()`: 参数replace\=False时为无放回抽样, \=True时为有放回抽样
- `.normal()`: 返回正态分布, local均值, 对应正态分布的中心, scale标准差, 对应分布宽度, 越大曲线越矮胖
- `.randn()`: 标准正态分布
- `.binomial(n, p, size)`: 二项分布, n种可能, p概率分布的阈值, size一组多少次伯努利试验
- `.poisson(lam, size)`: 泊松分布, 返回值表示一个单位内事件发生的次数

  - lam: 一个单位内发生事件的平均值
  - size: 采样的次数
- `.hypergeometric()`: 超几何分布: 从N个物中(包含M个特类)抽出n个物, 成功抽出特类的次数(不放回)
- `.exponential()`: 指数分布, 描述时间发生的时间长度间隔

# 元素操作

- `delete()`: 返回新数组, 具有沿删除的轴的子数组
- `insert()`: 沿给定轴在给定索引之前插入值
- `append()`: 将附加值加到数组的末尾
- `np.resize()`: 返回指定形状的新数组, 不改变原数组
- `trim_zeros()`: 修剪一维数组/序列中的前导零/尾随零
- `np.unique()`: 元素去重

  - arr: 必需, 输入数组
  - return_index: 若为True, 返回唯一值在原始数组中第一次出现的索引
  - return_inverse: 若为True, 返回原始数组中每个值对应的唯一值的索引
  - return_counts: 若为True, 返回每个唯一值在原始数组中的出现次数
  - axis: 按给定轴计算唯一值
- `unique()`: 查找数组的唯一元素
- `np.flip()`: 沿给定轴颠倒数组中的元素顺序
- `np.fliplr()`: 左右翻转数组
- `np.flipud()`: 上下翻转数组
- `np.roll()`: 沿给定轴滚动数组元素
- `np.rot90()`: 在轴指定平面中将阵列旋转90度
- `repeat`: 重复数组的元素
- `np.tile()`: 通过重复构造数组
- `.bincount()`: 返回数值范围内每个值的计数
- `np.any`​
- `np.all`​
- `np.where(condition, x, y)`: 若条件满足, 返回x, 否则返回y, 若不写x、y, 默认以元组形式返回每个符合条件的元素下标
- `np.argwhere`: 按条件查找, 返回符合条件的索引
- `np.compress()`: 筛选符合条件的数组
- `.clip(min, max)`: 将所有比min小的值设置为min 将所有比max大的值设置为max

# 数学函数

- `.abs()`​ `.round()`​ `.ceil()`​ `.floor()`​
- `.sqrt()`​ `.square()`​ `.power()`​
- `.exp()`​ `.log()`​ `.log2()`​ `.log10()`​
- `.sin()`​ `.cos()`​ `.tan()`​

# 聚合函数

关键参数：`keepdims=True`​用于保持结果的维度结构，输出的维度与原矩阵的维度一致

- `.median()`​
- `.mean()`: 算术平均值
- `.average()`: 加权平均值
- `.std()`​
- `.var()`​
- `.cov()`: 协方差
- `.min()`​
- `.argmin()`: 返回最小值对应下标
- `.max()`​
- `.argmax()`: 返回最大值对应下标
- `.convolve()`: 卷积运算
- `.ptp()`: 极差
- `.sum()`: axis参数不写时, 默认求所有数和; axis\=0表示对每一列多行求和(合并为一行); axis\=1表示对每一行多列求和(合并为一列), 数组中有nan时无法求和
- `.prod()`: 计算所有元素的乘积
- `.cumsum()`​ `.cumprod()`​
- .nan*()系列函数: 忽略nan值计算

  - nan: not a number, 数值类型之一, 表示空, 非正常数值, float类型
  - `.nansum()`: 排除nan求和
- `.percentile()`: 百分位数

# 矩阵操作

- 算数运算: `+`​、`-`​、`*`​、`/`​、`//`​(整除), 此处\*乘法, 只是对应位置元素的乘积, 不是矩阵的乘积
- `np.dot(n1, n2)`: 矩阵点乘, 左列数与右行数一致(m\*p和p\*n)
- `np.eye()`: 单位矩阵
- `np.linalg()`: 线性模型
- `np.round(np.linalg.det(arr))`: 矩阵的行列式
- `np.linalg.inv()`: 矩阵逆
- `np.diagonal()`: 查看对角线上的元素
- `np.trace()`: 计算矩阵的迹, 即对角线上元素之和
- `np.diag(arr)`: 提取对角线元素

  - arr若为一维, 生成以一维数组为主对角线的矩阵
  - arr若为二维, 输出主对角线元素为一维数组形式
- `np.diagflat(v)`: 创建二维数组, 使用展平的v作为对角线
- `np.linalg.matrix_rank()`: 矩阵的秩(满秩矩阵, 奇异矩阵)
- `np.mat()`: 将输入解释为矩阵
- `np.trill()`: 数组的下三角
- `np.triu()`: 数组的上三角
- `np.vander()`: 生成范德蒙矩阵
- 广播机制: 尽可能使矩阵间能运算, 为缺失的维度补维度, 缺失元素用已有值填充, 如复制已有行、列
- 解方程步骤: 

  ```python
  XTX = X.T.dot(X) 
  inv = np.linalg.inv(XTX) 
  np.dot(np.dot(inv,X.T), Y)
  ```
