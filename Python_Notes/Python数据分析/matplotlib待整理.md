# matplotlib待整理

## subplot()：创建子图布局

### 参数：

- 基本位置参数

  - ​**​`nrows`​**​ **和** **​`ncols`​**​：子图布局的**行数**和**列数**，索引从 `1`​ 开始，按从左到右、从上到下的**顺序排列**
  - ​**​`index`​**​：指定**当前操作的子图**在网格中的位置
  - **简化写法**：`subplot(231)`​ 等同于 `subplot(2, 3, 1)`​
- 布局控制参数

  - ​**​`sharex`​**​ **和** **​`sharey`​**​：控制子图间坐标轴的共享，需要对比数据确保坐标轴范围一致时使用

    - ​`sharex=True`​：所有子图共享同一 `x`​ 轴刻度（仅底部子图显示标签）
    - ​`sharey=True`​：所有子图共享同一 `y`​ 轴刻度（仅左侧子图显示标签）
  - ​**​`squeeze`​**​：控制返回的 Axes 对象维度

    - ​`squeeze=True`​（默认）：自动压缩单行/单列子图为 1D 数组，单子图时返回标量对象
    - ​`squeeze=False`​：始终返回 2D 数组，便于统一处理多子图
- 高级参数

  - ​**​`projection`​**​：指定子图的投影类型（如极坐标 `polar`​）
  - ​**​`facecolor`​**​：通过关键字参数设置子图属性，如背景颜色
  - ​**​`gridspec_kw`​**​：通过 `GridSpec`​ 对象调整子图布局比例

    - ​`width_ratios`​：定义列的宽度比例（如 `[2, 1]`​ 表示两列宽度比为 2:1）
    - ​`height_ratios`​：定义行的高度比例

### 不同调用方式：

|**调用方式**|**适用场景**|**示例**|
| ------| ------------------------------------| ------|
|​`subplot(nrows, ncols, index)`​|动态创建子图，逐步填充内容|​`subplot(2, 2, 1).plot(x, y)`​|
|​`subplots(nrows, ncols)`​|一次性创建全部子图，返回 Axes 数组|​`fig, axs = plt.subplots(2, 2)`​|
|​`add_subplot()`​|结合`Figure`​对象灵活添加子图|​`fig.add_subplot(2, 2, 1)`​|

## hist：绘制直方图

### 参数：

- 基本参数：

  - ​**​`x`​**​：输入数据，需为一维数组或列表（数值型或类别型）
  - ​**​`bins`​**​：设置直方图的区间划分方式，默认10，支持以下形式：

    - **整数**：表示等宽区间数量（如 `bins=20`​ 分为 20 个区间）
    - **序列**：自定义区间边界（如 `bins=np.arange(0, 100, 5)`​ 每 5 个单位划分一个区间）
    - **字符串**：自动优化区间数量（如 `bins='auto'`​）
  - ​**​`range`​**​：限制数据范围，超出范围的数据将被忽略
  - ​**​`density`​**​：控制纵轴显示形式

    - ​`density=False`​：显示频数（默认）
    - ​`density=True`​：显示概率密度（归一化为总面积 1）
- 布局与样式控制：

  - ​**​`histtype`​**​：可选值：

    - ​`'bar'`​：普通条形直方图（默认）
    - ​`'step'`​：仅绘制阶梯状边框
    - ​`'stepfilled'`​：填充阶梯状区域
    - ​`'barstacked'`​：堆叠式直方图（需配合 `stacked=True`​）
  - ​**​`orientation`​**​：调整直方图方向：

    - ​`'vertical'`​：垂直方向（默认）
    - ​`'horizontal'`​：水平方向
  - ​**​`color`​**​ **与** **​`edgecolor`​**​：设置填充色和边框颜色
  - ​**​`alpha`​**​：调整透明度（0 为透明，1 为不透明）
- 高级功能参数：

  - ​**​`weights`​**​：为数据点设置权重，用于绘制加权频数或概率
  - ​**​`cumulative`​**​：`=True`​则绘制累积直方图，显示累计频数或概率
  - ​**​`align`​**​：可选值：

    - ​`'mid'`​：区间中心对齐（默认）
    - ​`'left'`​ 或 `'right'`​：区间左/右边界对齐
  - ​**​`rwidth`​**​：控制柱子的宽度比例（相对于区间宽度），为(0, 1)间浮点数

### 示例代码：

```Python
 import matplotlib.pyplot as plt
 import numpy as np
 
 # 生成数据
 data = np.random.randn(1000)
 
 # 绘制直方图
 plt.hist(
     data,
     bins=30,
     range=(-3, 3),
     density=True,
     histtype='stepfilled',
     color='blue',
     edgecolor='black',
     alpha=0.6,
     label='Normal Distribution'
 )
 
 # 添加标签和标题
 plt.xlabel('Value')
 plt.ylabel('Density')
 plt.title('Customized Histogram')
 plt.legend()
 
 plt.show()
```

### 注意事项：

1. 绘制前需处理缺失值
2. 可通过多次调用 `plt.hist`​ 叠加不同数据集，需设置不同 `alpha`​ 增强可读性

## `plt.axis()`​

​`plt.axis('off')`​等同于`plt.axis(False)`​，但off形式更常见和直观

## 参数`cmap`​

​`cmap`​: colormap，颜色映射对象，用于将数据的数值映射到特定的颜色值，以便在图形中可视化数据的变化，常用`'viridis'`​、`'plasma'`​、`'RdYlBu'`​

- ​`plt.cm.RdYlBu`​：`plt.cm`​模块提供了所有可用的 colormap，`RdYlBu`​全名Red-Yellow-Blue，表示从红色到黄色再到蓝色的渐变，常用于表示数据变化

## `plt.imshow`​

将图像数据（如二维数组）**绘制**到当前的绘图区域（axes）中，但不会立即显示该图像，必须调用 `plt.show()`​ 来实际渲染并显示图形

**参数**：

- ​`X`​：要显示的图像数据，可以是二维（灰度图）或三维（RGB图像）数组
- ​`cmap`​：指定颜色映射（colormap），如 `'gray'`​、`'hot'`​、`'jet'`​ 等，用于控制图像的颜色显示
- ​`interpolation`​：设置插值方法（如 `'nearest'`​、`'bilinear'`​ 等），控制图像显示的平滑度
- ​`aspect`​：控制图像的纵横比，常用参数包括 `'equal'`​（保持原始比例）和 `'auto'`​（自动调整）

## `plt.show`​

一个阻塞函数，调用后会启动图形的事件循环，渲染并显示所有绘制的图形

​`plt.colorbar()`​：添加颜色条，指示值的范围

​`plt.scatter()`​

参数s：控制散点大小size，指点的面积而不是直径，可为一个数值（所有点同等大小）或一个数组（每个点一个大小）

```Python
 x = [1, 2, 3, 4]
 y = [10, 20, 30, 40]
 sizes = [20, 50, 80, 200]
 
 plt.scatter(x, y, s=sizes)
 plt.show()
```
