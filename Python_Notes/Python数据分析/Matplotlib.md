![image](../../assets//截屏2025-01-14%2022.01.31-20250114220138-k6fsink.png)

# Matplotlib

核心模块：pyplot模块

核心类：

- Figure：图形对象，整个图像和其中的所有子图（Axes）
- Axes：子图，表示一个坐标系，图形中的每个坐标系通常包含一个坐标轴（x 和 y）和数据的绘制
- Artist：在 Axes 上绘制的元素，例如线条、标签、刻度、文本等

# 配置项

- 导包: 

  ```python
  import matplotlib as plt 
  import seaborn as sns
  ```
- `%matplotlib inline`: 在notebook中绘制图形, 不再需要手动调用`plt.show()`​
- 堆叠: stacked\=True
- 透明度: alpha\=n
- 字体: `plt.rcParams['font.sans-serif'] = ['SimHei']`​
- 字体大小: fontsize\=
- 拆分为多图: `.diff().hist/bar/……()`​
- 设置两个y轴: 

  ```python
  .plot(secondary_y=['A', 'B']) 
  .setylabel('CD')
  
  # 或
  ax1 = plt.gca() 
  ax2 = ax1.twinx()
  ```
- 单独配置大小: `plt.*(size=n)`​
- 图例位置: `plt.legend(loc=n)`​
- 网格: `grid=True`​
- 标题: title\=' '
- 轴标签: xlabel\=' ', ylabel\=' '
- 图片大小: figsize\=()
- 图片质量: `plt.rcParams['savefig.dpi'] = 100`​
- 子图：`plt.subplot()`​或`plt.subplots()`​(更灵活)

  - `plt.subplot(nrows, ncols, index)`: 在现有的Figure中创建一个子图并激活，只返回一个Axes对象
  - `plt.subplots()`​：返回元组`(fig, axs)`

    - `fig`：Figure对象，代表整个图形窗口
    - `axs`：动态类型，包含所有子图轴对象，单子图时为Axes对象(非数组)，单行/单列时返回一维数组`(n，)`，多行多列时返回二维数组`(m, n)`
        - `.subplots(..., squeeze=False)`：强制返回二维数组，统一维度
  - `axes.flatten()`​：将二维数组axes转换成一维数组，对子图进行相同的操作时，使用flatten可方便地遍历所有子图的轴对象而不再需要分开处理每一行
  - `axes.cla()`: 清除当前轴，保留坐标轴、标签、刻度线
  
    ```python
    fig, axes = plt.subplots(2, 3)
    ```
- 解决坐标轴负数的负号显示问题: `plt.rcParams['axes.unicode_minus'] = False`​

- 折线图: 

  - `.plot()`​, subplots\=True每列一个子图
  - `.T.plot()`: 每一行一根线
- 柱状图: `.plot(kind='bar')`​或`.plot.bar()`​, bar换为barh: 水平, 条形图, stacked\=True: 堆叠
- 直方图: `.plot(kind='hist')`​, bins\=n箱数, density\=True把频数转换为概率
- 密度曲线图: 核密度估计, 弥补直方图精度缺失, `.plot(kind='kde')`​
- 饼图: `.plot(kind='pie')`​或`.plot.pie()`​, autopct\='%.1f%%', subplots\=True子图
- 散点图: `.plot.scatter()`​或`.plot(kind='scatter', x=, y=)`​

  - x\='', y\='': 使用指定列作为X、Y轴
  - s\=df[]\*200: 点大小由值决定; s\=n: 点大小固定
  - c\=' ': 点颜色由值决定
  - marker\='o'
  - linewidths\=n
  - edgecolors\='black': 点增加黑色边缘线
  - label\=: 分组
- 面积图: `.plot(kind='area')`​或`.plot.area()`​, stacked\=True: 堆叠
- 箱型图: `.plot(kind='box')`​或`.plot.box()`​

  - color\={'boxes': 'DarkGreen', 'whiskers': 'DarkOrange', 'medians': 'DarkBlue',  'caps': 'Gray'}
  - sym\='r+'
  - vert\=False: 水平格式, 默认竖直格式
- 六边形箱图: `.plot.hexbin()`​, x\=' ', y\=' ', gridsize\=n
- 统计图: 

  - 条形图: 

    - `.plot(kind='bar')`​
    - `.set_xlabel('')`​、`.set_ylabel('')`: 坐标轴标签
    - `.set_title('')`: 标题
  - 饼: 

    ```python
    fig, ax=plt.subplots(figsize=(5, 5))
    ax.pie(df, startangle=90, explode=[0.05, 0.05...], autopct\='%.1f%%', labels=df.index)
    ```
  - 直方图: 

    - `plt.hist(df[ ], bins, facecolor, edgecolor, alpha)`​
    - `plt.xlabel('')`​、`plt.ylabel('')`​、`plt.title('')`​
  - 线图:

    - 直线图:

      ```python
      ax = df.plot(kind='line', secondary_y=['频率']) 
      ax.set_xlabel('') 
      ax.set_ylabel('') 
      ax.right_ax.set_ylabel('频率') 
      ax.set_title('')
      ```
    - 曲线图:

      ```python
      import numpy as np 
      from scipy.interpolate import interpld 
      x = np.linspace(df.index.min(), df.index.max(), 300) 
      func = interpld(df.index, df['频数'], kind='cubic') 
      y = func(x) 
      func2 = interpld(df.index, df['频率'], kind='cubic') 
      y2 = func(x) 
      fig, ax = plt.subplots() 
      ax.set_title('') 
      ax.plot(x, y) 
      ax.set_xlabel('区间') 
      ax.set_ylabel('频数') 
      ax2 = ax.twinx() 
      ax2.plot(x, y2) 
      ax2.set_ylabel('频率')
      ```

# Seaborn

matplotlib的子库

- 配置项: 

  - 绘图主题: `sns.set_palette()`​, 'pastel'、'Blues\_r'、'magma'
  - 绘图后端引擎: `pd.set_option("plotting.backend","plotly")`​, 修改引擎为plotly
  - 画布风格: `sns.set_style()`​, 'white'、'whitegrid'、'dark'、'darkgrid'

    - {'sans-serif': ['SimHei': 'Arial']}
  - 图例: `plt.legend()`​

    - 图例位置: loc\=  

      ![截屏2025-01-14 22.57.35](../../assets//截屏2025-01-14%2022.57.35-20250114225738-t1bw3as.png)​
    - fontsize\=
    - frameon\=False: 去掉图例边框
    - edgecolor\=''
    - facecolor\='': 图例背景颜色
    - title\=: 标题
  - 获取当前对象:

    - `plt.gca()`: 当前坐标轴
    - `plt.gcf()`: 当前图表
- 柱状图: `sns.barplot()`​, x\='', y\='', color\=, ax\=plt.gca(), hue\=条件
- 箱图: `sns.boxplot()`​
- 小提琴图: `sns.violinplot()`​
- 海带图: `sns.stripplot()`​
- 蜂群图: `sns.swarmplot()`​
- 分面网格: `sns.catplot()`​, row\=分面依据, hue\=, x\=, y\=
- 散步图: `sns.scatterplot()`​, x\=, y\=, hue\=
- 散点矩阵图: `sns.pairplot()`​, hue\=
- 热力图: `sns.heatmap()`​, annot\=True显示数字, cmap\='Summer'颜色风格
