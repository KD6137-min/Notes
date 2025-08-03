![image](../assets/截屏2025-01-14%2015.11.22-20250114151131-k3nhehx.png)

# 概述

## 一级包结构

- 数据处理：
    - 数据预处理：preprocessing, feature_selection, impute
    - 数据集：datasets
- 算法：
    - 有监督：linear_model, svm, tree, ensemble, neighbors
    - 无监督：cluster, decomposition, manifold, mixture
- 模型评估：
    - 交叉验证和网格搜索：model_selection
    - 评估指标：metrics
- 工具：
    - pipeline 结合多个步骤形成完整的流程
    - utils 提供各种工具函数

![截屏2025-01-01 23.04.16](../assets/截屏2025-01-01%2023.04.16-20250101230420-eax2wgs.png)

## 模型的保存&加载

joblib库, 适用于跨项目共享模型、模型持久化、部署模型

- 导入: `from joblib import dump, load`​
- `dump(model, path)`: 将模型序列化并保存到指定路径, path扩展名为`.joblib`​

  - 适用于保存较大的模型或矩阵
  - `pickle`​更通用, 可序列化更多类型的对象, 但效率较低
- `load(path)`: 从文件加载模型并反序列化, 返回加载的模型, 赋值给变量后可直接使用

## 鸢尾花数据集

iris dataset, 150条样本

- 3种类型: 山鸢尾、多彩鸢尾、维吉尼亚鸢尾, 每种各有50条样本
- 1个类别标签: 0、1、2三个值, 对应3种类型
- 4个特征: 花萼长度、花萼宽度、花瓣长度、花瓣宽度, 单位厘米

```python
from sklearn.datasets import load_iris
# 加载鸢尾花数据集
iris = load_iris()
# 查看数据集介绍
print(iris.DESCR)

# 特征(150行4列的二维数组, 分别是花萼长、花萼宽、花瓣长、花瓣宽)
X = iris.data
# 标签(150个元素的一维数组, 包含0、1、2三个值分别代表三种鸢尾花)
y = iris.target
```

## 汽车MPG数据集

非内置, 需下载/联网加载

```python
import ssl
import pandas as pd

ssl._create_default_https_context = ssl._create_unverified_context
df = pd.read_csv('https://archive.ics.uci.edu/static/public/9/data.csv')
df.info()
```

9个属性中, 8个输入变量, 1个输出变量(mpg)

|属性名称|描述|
| ----------| -------------------------------------------------------------------------------|
|*car_name*|汽车的名称, 字符串, 这个属性对建模暂时没有帮助|
|*cylinders*|气缸数量, 整数|
|*displacement*|发动机排量(立方英寸), 浮点数|
|*horsepower*|马力, 浮点数, 有空值需要提前处理|
|*weight*|汽车重量(磅), 整数|
|*acceleration*|加速(0 - 60 mph所需时间), 浮点数|
|*model_year*|模型年份(1970年 - 1982年), 这里用的是两位的年份|
|*origin*|汽车来源(1 \= 美国, 2 \= 欧洲, 3 \= 日本), 123视为三种类别而不是整数|
|*mpg*|车辆的燃油效率, 每加仑行驶的里程(目标变量)|

预处理: 

- 删除car_name(用不上), 计算特征之间的相关性和方差膨胀因子VIF检测共线性问题, 剔除与目标值相关性弱的特征(减少模型复杂度和过拟合风险)

  ```python
  # 删除指定的列
  df.drop(columns=['car_name'], inplace=True)
  # 计算相关系数矩阵
  df.corr()	# 默认计算皮尔逊相关系数(适合正态总体连续值)
  			# method=spearmean计算皮尔曼秩相关, method=kendall计算肯德尔系数
  ```
- 删除horsepower的空值, 将origin字段处理成独热编码(One-Hot Encoding)

  - 独热编码: 处理分类变量的常见编码方式, 将每个分类变量转换为若干个新的二元特征(`0`​或` 1`​)来表示, 从而使得这些变量可以输入到机器学习模型中, 对于无序类别独热编码是非常有效, 不会引入任何虚假的顺序关系, 类别取值过多时会产生大量新特征, 导致维度增加, 影响计算性能和效率

    - 只有三类时, 可只保留两列, 两列均为0时表示为第三个类别

  ```python
  # 删除有缺失值的样本
  df.dropna(inplace=True)
  # 将origin字段处理为类别类型
  df['origin'] = df['origin'].astype('category') 
  # 将origin字段处理为独热编码
  df = pd.get_dummies(df, columns=['origin'], drop_first=True)
  df
  ```

## 数据集划分

- 手动划分:

  ```python
  # 将特征和标签堆叠到同一个数组中
  data = np.hstack((X, y.reshape(-1, 1)))
  # 通过随机乱序函数将原始数据打乱
  np.random.shuffle(data)
  # 选择80%的数据作为训练集
  train_size = int(y.size * 0.8)
  train, test = data[:train_size], data[train_size:]
  X_train, y_train = train[:, :-1], train[:, -1]
  X_test, y_test = test[:, :-1], test[:, -1]
  ```
- `train_test_split()`: 传入特征和标签, 指定`train_size`​或`test_size`​即可, 返回一个四元组(用于训练的特征、用于训练的标签、用于测试的特征、用于测试的标签)

  ```python
  from sklearn.model_selection import train_test_split
  # random_state为随机数种子
  X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.8, random_state=3)
  ```

## 模型评估

不能只看准确率，推荐用混淆矩阵

- `confusion_matrix()`​和`classification_report()`: 输出混淆矩阵和评估报告

  ```python
  from sklearn.metrics import classification_report, confusion_matrix
  
  # 输出分类模型混淆矩阵
  print('混淆矩阵: ')
  print(confusion_matrix(y_test, y_pred))
  # 输出分类模型评估报告
  print('评估报告: ')
  print(classification_report(y_test, y_pred))
  ```
- `ConfusionMatrixDisplay()`: 可视化方式输出混淆矩阵

  ```python
  import matplotlib.pyplot as plt
  from sklearn.metrics import ConfusionMatrixDisplay
  
  # 创建混淆矩阵显示对象
  cm_display_obj = ConfusionMatrixDisplay(confusion_matrix(y_test, y_pred), display_labels=iris.target_names)
  # 绘制并显示混淆矩阵
  cm_display_obj.plot(cmap=plt.cm.Reds)
  plt.show()
  ```
- 绘制ROC曲线并显示AUC值:

  ```python
  from sklearn.metrics import roc_curve, auc
  from sklearn.metrics import RocCurveDisplay
  
  # 手动构造一组真实值和对应的预测值
  y_test_ex = np.array([0, 0, 0, 1, 1, 0, 1, 1, 1, 0])
  y_pred_ex = np.array([1, 0, 0, 1, 1, 0, 1, 1, 0, 1])
  # 通过roc_curve函数计算出FPR(假正例率)和TPR(真正例率)
  fpr, tpr, _ = roc_curve(y_test_ex, y_pred_ex)
  # 通过auc函数计算出AUC值并通过RocCurveDisplay类绘制图形
  RocCurveDisplay(fpr=fpr, tpr=tpr, roc_auc=auc(fpr, tpr)).plot()
  plt.show()
  ```

## 参数调优

1. 网格搜索(Grid Search): 通过穷举法遍历给定的超参数空间, 找到最优的超参数组合

    ```python
    from sklearn.model_selection import GridSearchCV
    
    # 网格搜索交叉验证
    gs = GridSearchCV(
        estimator=KNeighborsClassifier(),
        param_grid={
            'n_neighbors': [1, 3, 5, 7, 9, 11, 13, 15],
            'weights': ['uniform', 'distance'],
            'p': [1, 2]
        },
        cv=5
    )
    gs.fit(X_train, y_train)
    
    # 获得最优参数和评分
    print('最优参数:', gs.best_params_) 	# 返回字典
    print('评分:', gs.best_score_)
    
    # 使用训练好的最优模型进行预测
    gs.predict(X_test)
    ```
    
6. 交叉验证Cross Validation: 

    - **K-Fold交叉验证**: 

      ```python
      from sklearn.model_selection import KFold
      # 示例数据
      X = np.array([[1, 2], [3, 4], [5, 6], [7, 8], [9, 10]])
      y = np.array([0, 1, 0, 1, 0])
      # 创建KFold对象
      kf = KFold(n_split=3, shuffle=True, random_state=42)
      # n_split: 折数，默认5
      # shuffle: 打乱数据顺序
      # random_state: 随机种子
      
      # 直接遍历分割结果
      for train_index, test_index in kf.split(X):
          print("Train:", train_index, "Test:", test_index)
          X_train, X_test = X[train_index], X[test_index]
          y_train, y_test = y[train_index], y[test_index]
          # 在此训练和验证模型
      ```
    - 高级封装：`cross_val_score`​, 一步到位，评估模型的平均性能
    
      ```python
      from sklearn.ensemble import RandomForestClassifier
      from sklearn.model_selection import cross_val_score
      
      # 创建模型
      model = RandomForestClassifier()
      
      # 一键完成 K 折验证（默认分 5 折）
      scores = cross_val_score(model, X, y, cv=3)  # cv=3 表示 3 折
      
      # 输出结果
      print("Accuracy per fold:", scores)
      print("Mean accuracy:", scores.mean())
      ```
    - 针对分类问题的分层抽样：`StratifiedKFold`​, 数据类别分布不均衡时使用，保证每一折的类别分布一致
    
      ```python
      from sklearn.model_selection import StratifiedKFold
      
      skf = StratifiedKFold(n_splits=3, shuffle=True, random_state=42)
      for train_index, test_index in skf.split(X, y):
          # 用法与 KFold 相同
      ```



---



# k最近邻算法

```python
from sklearn.neighbors import KNeighborsClassifier

# 创建模型
model = KNeighborsClassifier()
# 训练模型
model.fit(X_train, y_train)
# 预测结果
y_pred = model.predict(X_test)

# 准确率
print(model.score(X_test, y_test))
```

`KNeighborsClassifier()`: 

- `n_neighbors`: k值
- `weights`: 权重计算方法
    - `uniform`: 默认, 所有样本权重相同
    - `distance`: 距离越近权重越高
    - 可传入自定义函数确定样本权重
- `algorithm`: `auto`​、`ball_tree`​、`kd_tree`​、`brute`​四选一, 默认值为`auto`​
    - `ball_tree`: 一种树形结构, 基于球体划分的方法将数据点分配到层次化的树结构中, 在高维数据和稀疏数据场景下有较好的性能
    - `kd_tree`: 一种树形结构, 通过选择一个维度将空间划分为若干个子区域再进行搜索, 从而避免跟所有的邻居进行比较, 对于低维度和空间分布均匀的数据有较好的效果, 在高维空间中会遇到维度灾难问题
    - `auto`: 默认, 选项是根据输入数据的维度自动选择`ball_tree`​或`kd_tree`​
    - `brute`: 暴力搜索算法(穷举法), 适合处理小数据集
- `leaf_size`: 使用`ball_tree`或`kd_tree`算法时, 该参数用于限制树结构叶子节点最大样本数量(影响树的构建和节点查找的性能), 默认值为`30`
- `p`: 闵可夫斯基距离公式中的`p`, 默认值为2, 计算欧氏距离

`.fit(X_train, y_train)`: 训练, 不接受一维数组作为一参, 只有一维也需`np.reshape(-1, 1)`​

`.score()`: 评估

`.predict()`: 预测

`.predict_proba()`: 各种取值的概率

## kNN回归

根据离新实例最近的k个邻居的目标值预测新实例的目标值, 通常使用平均/加权平均处理邻居目标值, 噪声敏感

`KNeighborRegressor`​创建kNN回归模型

```python
from sklearn.neighbors import KNeighborsRegressor

# 创建模型
model = KNeighborsRegressor()
# 训练模型
model.fit(X, y)
# 预测结果
y_pred = model.predict(X)

# 可视化
# 原始数据散点图
plt.scatter(X, y, color='navy')
# 预测结果折线图
plt.plot(X, y_pred, color='coral')
plt.show()
```

# 决策树和随机森林

tree模块, `DecisionTreeClassifier`​用于分类, `DecisionTreeRegressor`​用于回归

```python
from sklearn.tree import DecisionTreeClassifier

# 创建模型
model = DecisionTreeClassifier()
# 训练模型
model.fit(X_train, y_train)
# 预测结果
y_pred = model.predict(X_test)
```

1. `criterion`: 特征选择(数据分裂质量评估)的标准, `'gini'`​基尼指数或`'entropy'`​信息增益
2. `max_depth`: 树的最大深度, 默认`None`​, 不设置该参数则有过拟合风险
3. `min_samples_split`: 一个内部结点再次分裂所需的最小样本数, 默认为`2`​

    - 设为整数, 表示最小样本数
    - 设为浮点数, 表示占总样本数的比例
4. `min_samples_leaf`: 叶结点所需的最小样本数, 默认为`1`​

    - 设为较大值可平滑模型, 降低过拟合风险
    - 可设为整数或浮点数(数量/比例)
5. `max_features`: 用于最佳分裂的特征数, 默认为`None`​

    1. 设为整数, 表示选择固定数量的特征
    2. 设为浮点数, 表示选择特征的比例
    3. 设为字符串: 

        1. `'auto'`​和`'sqrt'`​表示将总的特征数量求平方根, 用平方根的值作为选择特征的数量
        2. `'log2'`​表示将总的特征数量求对数, 用对数值作为选择特征的数量
6. `class_weight`: 指定类别的权重, 用于处理类别不平衡问题, 默认为`None`​, 可用字典手动设置每个类别的权重, 也可用`'balanced'`​让模型自动调整
7. `splitter`: 选择分裂结点的策略, 默认为`'best'`​最佳分裂, 可选`'random'`​随机分裂
8. `max_leaf_nodes`: 限制叶结点的最大数量, 防止树结构过于复杂
9. `min_impurity_decrease`: 结点分裂所需的最小不纯度降低值
10. `ccp_alpha`: 后剪枝成本复杂度公式中的$\small{\alpha}$值, 小$\small{\alpha}$值允许更复杂的树, 大$\small{\alpha}$值倾向于简单树, 调整$\small{\alpha}$可找到最佳的复杂度和误差之间的平衡点

## 可视化

```python
import matplotlib.pyplot as plt
from sklearn.tree import plot_tree

plt.figure(figsize=(12, 10))
plot_tree(
    decision_tree=model,               # 决策树模型
    feature_names=iris.feature_names,  # 特征的名称
    class_names=iris.target_names,     # 标签的名称
    filled=True                        # 用颜色填充
)
plt.show()
```

![截屏2025-01-23 00.43.15](../assets/截屏2025-01-23%2000.43.15-20250123004318-mmmgwz4.png)

## 随机森林

`ensemble`​模块的`RandomForestClassifier`​类

```python
from sklearn.ensemble import RandomForestClassifier

gs = GridSearchCV(
    estimator=RandomForestClassifier(n_jobs=-1),
    param_grid={
        'n_estimators': [50, 100, 150],
        'criterion': ['gini', 'entropy'],
        'max_depth': np.arange(5, 10),
        'max_features': ['sqrt', 'log2'],
        'min_samples_leaf': np.arange(1, 11),
        'max_leaf_nodes': np.arange(5, 15)
    },
    cv=5
)
gs.fit(X_train, y_train)
```

1. `estimator`: 用多少决策树来构造森林, 更多的树通常会带来更好的预测效果, 但也会增加计算成本
2. `boostrap`: 是否使用Bootstrap抽样, 默认为`True`​, 表示在构建每棵树时使用有放回抽样
3. `n_jobs`: 用于并行运行的任务数, 默认为`None`​, 表示使用单个处理器核心, 设为`-1`​则可使用所有可用的处理器核心



---



# 朴素贝叶斯

naive_bayes模块, 五个朴素贝叶斯算法的变体

|分类器|特征类型|主要假设|
| --------| ----------| ----------------------------------------|
|​`BernoulliNB`​|二元特征|特征服从Bernoulli分布|
|​`CategoricalNB`​|类别特征|特征服从多项式分布, 常用于处理类别数据|
|​`ComplementNB`​|计数特征|利用补集概率, 常用于处理不平衡数据集|
|​`GaussianNB`​|连续特征|特征服从高斯分布|
|​`MultinomialNB`​|计数特征|特征服从多项式分布, 常用于文本分类|

```python
from sklearn.naive_bayes import GaussianNB
from sklearn.metrics import classification_report

model = GaussianNB()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print(classification_report(y_test, y_pred))

# 模型对每个样本给出的标签概率值
model.predict_proba(X.test).round(2)
```



---



# 回归

## 线性回归实现

### 最小二乘法

`linear_model`​模块`LinearRegression`​

```python
from sklearn.linear_model import LinearRegression

model = LinearRegression()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

# 查看线性回归模型的参数
print('回归系数:', model.coef_)
print('截距:', model.intercept_)
```

### 梯度下降法

`linear_model`​模块`SGDRegressor`​

```python
from sklearn.linear_model import SGDRegressor
from sklearn.preprocessing import StandardScaler

# 对特征进行选择和标准化处理
scaler = StandardScaler()
scaled_X = scaler.fit_transform(X[:, [1, 2, 3, 5]])
# 重新拆分训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(scaled_X, y, train_size=0.8, random_state=3)

# 模型的创建、训练和预测
model = SGDRegressor()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print('回归系数:', model.coef_)
print('截距:', model.intercept_)

# 模型评估
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)
print(f'均方误差: {mse:.4f}')
print(f'决定系数: {r2:.4f}')
```

- `loss`: 指定优化目标(损失函数), 默认为`'squared_error'`​(最小二乘法), 可选`'huber'`​(适用于对异常值更鲁棒的回归模型)、`'epsilon_insensitive'`​、`'squared_epsilon_insensitive'`​
- `penalty`: 指定正则化方法, 用于防止过拟合, 默认为`'l2'`​(L2正则化), 可选`'l1'`​(L1正则化)、`'elasticnet'`​(弹性网络)、`None`​(不使用正则化)
- `alpha`: 正则化强度的系数, 控制正则化项的权重, 默认为`0.0001`​, 较大的`alpha`​加重正则化影响, 从而限制模型复杂度, 较小的值会让模型更关注训练数据的拟合
- `l1_ratio`: `penalty='elasticnet'`​时, 控制L1和L2正则化之间的权重, 默认为`0.15`​, 取值范围为`[0, 1]`​(`0`​ 表示完全使用L2, `1`​表示完全使用L1)
- `tol`: 优化算法的容差, 即判断收敛的阈值, 默认为`1e-3`​, 当目标函数的改变量小于`tol`​时, 训练会提前终止, 若希望训练更加精确, 可以适当降低`tol`​
- `learning_rate`: 指定学习率的调节策略, 默认值为`'constant'`​, 表示使用固定学习率, 具体的值由 `eta0`​指定, 可选:

  - `'optimal'`: 基于公式`eta = 1.0 / (alpha * (t + t0))`​自动调整
  - `'invscaling'`: 按`eta = eta0 / pow(t, power_t)`​缩放学习率
  - `'adaptive'`: 动态调整, 误差减少时保持当前学习率, 否则减小学习率
- `eta0`: 初始学习率, 默认为`0.01`​, 当`learning_rate='constant'`​或其他策略使用时, `eta0`​决定了初始更新步长
- `power_t`: 当`learning_rate='invscaling'`​时, 控制学习率衰减速度, 默认值为`0.25`​, 较小的值会让学习率下降得更慢, 从而更长时间地关注全局优化
- `early_stopping`: 是否启用早停机制, 默认为`False`​, 若设为`True`​, 模型会根据验证集性能自动停止训练, 防止过拟合
- `validation_fraction`: 指定用作验证集的训练数据比例, 默认为`0.1`​, 当`early_stopping=True`​时, 该参数会起作用
- `max_iter`: 训练的最大迭代次数, 默认为`1000`​, 当数据较大或学习率较小时, 可能需要增加迭代次数以保证收敛
- `shuffle`: 是否在每个迭代轮次开始时打乱训练数据, 默认为`True`​, 表示打乱数据, 打乱数据有助于提高模型的泛化能力
- `warm_start`: 是否使用上次训练的参数继续训练, 默认为`False`​, 设为`True`​可在已有模型的基础上进一步优化
- `verbose`: 控制训练过程的日志输出, 默认为`0`​, 可以设置为更高值以观察训练进度

## 回归模型评估

```python
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score

mse = mean_squared_error(y_test, y_pred)
mae = mean_absolute_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

print(f'均方误差: {mse:.4f}')
print(f'平均绝对误差: {mae:.4f}')
print(f'决定系数: {r2:.4f}')
```

## 引入正则化项的回归

### 岭回归

`linear_model`​模块`Ridge`​类

```python
from sklearn.linear_model import Ridge

model = Ridge()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print('回归系数:', model.coef_)
print('截距:', model.intercept_)

mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)
print(f'均方误差: {mse:.4f}')
print(f'决定系数: {r2:.4f}')
```

### 套索回归

`linear_model`​模块`Lasso`​类

```python
from sklearn.linear_model import Lasso

model = Lasso()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print('回归系数:', model.coef_)
print('截距:', model.intercept_)

mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)
print(f'均方误差: {mse:.4f}')
print(f'决定系数: {r2:.4f}')
```

## 多项式回归

`compose`​模块`ColumnTransformer`: 只对一部分特征添加高次项

`preprocessing`​模块`PolynomialFeatures`​类: 进行特征预处理, 将原始特征扩展为多项式特征, 从而将线性模型转换为具有高次项的模型, 但引入高次项后过拟合风险大大增加(尤其数据量小时), 且会让特征范围变得很大, 需进行标准化处理

`PolynomialFeatures()`​超参数:

- `degree`: 设置多项式的最高次项
- `interaction_only`: 默认为`False`​, 设为`True`​则只生成交互项(如$\small{x_{1}x_{2}}$), 不生成单独的高次项(如$\small{x_{1}^{2}}$、$\small{x_{2}^{2}}$)
- `include_bias`: 默认为`True`​, 表示包括常数项(通常为1), 设为`False`​则不包括常数项

```python
from sklearn.preprocessing import PolynomialFeatures

# 特征预处理
poly = PolynomialFeatures(degree=2)
x_ = poly.fit_transform(x_)

model = LinearRegression()
model.fit(x_, y)
y_pred = model.predict(x_)

r2 = r2_score(y, y_pred)
print(f'决定系数: {r2:.4f}')
```

## 逻辑回归

```python
from sklearn.datasets import make_classification	# 用于生成一组模拟数据
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report

# 生成1000条样本数据, 每个样本包含6个特征
X, y = make_classification(n_samples=1000, n_features=6, random_state=3)
# 将1000条样本数据拆分为训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.8, random_state=3)

# 创建和训练逻辑回归模型
model = LogisticRegression()
model.fit(X_train, y_train)

# 对测试集进行预测并评估
y_pred = model.predict(X_test)
print(classification_report(y_test, y_pred))
```

`LogisticRegression()`​超参数: 

- `penalty`: 指定正则化类型, 用于控制模型复杂度, 防止过拟合, 默认为`l2`​
- `C`: 正则化强度的倒数, 默认为`1.0`​, 较小的`C`​值会加强正则化(更多限制模型复杂度), 较大的`C`​值会减弱正则化(更注重拟合训练数据)
- `solver`: 指定优化算法, 默认值为`lbfgs`​, 可选: 

  - `'newton-cg'`​、`'lbfgs'`​、`'sag'`​、`'saga'`: 支持L2和无正则化
  - `'liblinear'`: 支持L1和L2正则化, 适用于小型数据集
  - `'saga'`: 支持L1、L2和ElasticNet, 适用于大规模数据
- `multi_class`: 指定多分类问题的处理方式, 默认为`'auto'`​, 根据数据选择`'ovr'`​或`'multinomial'`​, 

  - `'ovr'`: 一对多策略, 适合二分类或多分类的基础情况
  - `'multinomial'`: 多项式回归策略, 适用于多分类问题, 需与`'lbfgs'`​、`'sag'`​或`'saga'`​搭配使用
- `fit_intercept`: 是否计算截距(偏置项), 默认为`True`​
- `class_weight`: 类别权重, 处理类别不平衡问题, 默认为`None`​, 设为`'balanced'`​可根据类别频率自动调整权重



---



# K-Means聚类

`cluster`​模块`KMeans`​类

```python
from sklearn.cluster import KMeans

# 创建KMeans对象
km_cluster = KMeans(
    n_clusters=3,       # k值(簇的数量)
    max_iter=30,        # 最大迭代次数
    n_init=10,          # 初始质心选择尝试次数
    init='k-means++',   # 初始质心选择算法
    algorithm='elkan',  # 是否使用三角不等式优化
    tol=1e-4,           # 质心变化容忍度
    random_state=3      # 随机数种子
)
# 训练模型
km_cluster.fit(X)

print(km_cluster.labels_)           # 分簇的标签
print(km_cluster.cluster_centers_)  # 各个质心的位置
print(km_cluster.inertia_)          # 样本到质心的距离平方和
```

`KMeans()`​超参数: 

- `n_clusters`: 指定聚类的簇数, 即K值, 默认为`8`​
- `max_iter`: 最大迭代次数, 默认为`300`​, 控制每次初始化中K-Means迭代的最大步数
- `init`: 初始化质心的方法, 默认为`'k-means++'`​, 表示从数据中**多次**随机选取K个质心, 每次都计算这一次选中的中心点之间的距离, 然后取距离最大的一组作为初始化中心点, 推荐使用, 若设为`'random'`​则随机选择初始质心
- `n_init`: 和`init`​参数配合, 指定算法运行的初始化次数, 默认为`10`​
- `algorithm`: K-Means的计算算法, 默认为`'lloyd'`​, 可选`'elkan'`​, 表示基于三角不等式的优化算法, 适用于K值较大的情况, 计算效率较高
- `tol`: 容忍度, 控制算法的收敛精度, 默认为`1e-4`​, 数据集较大时, 可适当增大此值以加快收敛速度



---



# 集成学习算法

## AdaBoost

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import AdaBoostClassifier
from sklearn.metrics import classification_report

# 数据集的加载和划分
iris = load_iris()
X, y = iris.data, iris.target
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.8, random_state=3)

# 初始化弱分类器(决策树桩)
base_estimator = DecisionTreeClassifier(max_depth=1)
# 初始化 AdaBoost 分类器
model = AdaBoostClassifier(base_estimator, n_estimators=50)
# 训练模型
model.fit(X_train, y_train)
# 预测结果
y_pred = model.predict(X_test)

# 输出评估报告
print(classification_report(y_test, y_pred))
```

- `n_estimators`: 指定要训练的基学习器(弱分类器)的数量, 默认为50
- `learning_rate`: 控制每个基学习器在最终模型中的贡献大小, 即学习率, 默认值为1.0
- `algorithm`: 决定AdaBoost的训练算法

  - `'SAMME'`: 用于多类分类问题的算法, 采用加法模型
  - `'SAMME.R'`: 基于Real AdaBoost的算法, 用于加权的二分类和多分类问题, 使用了一个加权的重新采样策略
- `base_estimator`: 基学习器, 默认为`None`​, 使用`max_depth=1`​的`DecisionTreeClassifier`​作为基学习器

## GBDT

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.ensemble import GradientBoostingClassifier
from sklearn.metrics import classification_report

# 数据集的加载和划分
iris = load_iris()
X, y = iris.data, iris.target
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.8, random_state=3)

# 初始化 GBDT 分类器
model = GradientBoostingClassifier(n_estimators=32)
# 训练模型
model.fit(X_train, y_train)
# 预测结果
y_pred = model.predict(X_test)

# 输出评估报告
print(classification_report(y_test, y_pred))
```

`GradientBoostingClassifier()`：​

- `loss`: 指定用于分类任务的损失函数, 默认为`deviance`​, 表示使用对数损失函数, 可选`exponential`​, 表示使用指数损失函数(AdaBoost的损失函数)
- `learning_rate`: 控制每棵树对最终预测结果的影响程度, 默认为0.1, 较小的学习率通常会带来更好的泛化能力, 但需要更多的弱分类器(即更多的树)来拟合训练数据, 如果学习率设置得太大, 则可能会导致模型欠拟合
- `n_estimators`: 指定要训练的基学习器(通常是决策树)的数量, 默认为100
- `subsample`: 控制每棵树训练时使用的数据比例, 默认值为1.0(即使用所有数据), 可在训练每棵树时随机选择部分样本, 从而增加模型的随机性减少过拟合, 一般为0.8或0.9
- `criterion`: 用于控制分裂时的分裂标准, 默认为`'friedman_mse'`​, 决定如何选择每个节点的最佳划分方式, 可选: 

  - `'friedman_mse'`: 基于均方误差(MSE)的改进版本, 旨在减小对不平衡数据的敏感性, 并且在一些实际分类任务中, 能够比传统的MSE产生更好的结果
  - `'mse'`: 使用传统的均方误差(MSE)来评估每个分裂点的质量, MSE越小, 说明当前节点的分裂越好
  - `'mae'`: 使用平均绝对误差(MAE)来评估分裂的质量, 对异常值不那么敏感, 但在实际应用中通常较少使用
- `validation_fraction`: 用于指定训练过程中的验证集比例, 用于执行早期停止(early stopping), 避免过拟合, 可与`n_iter_no_change`​配合在验证集上评估性能, 当连续若干轮没有改进时提前停止训练

## XGBoost

```python
import matplotlib.pyplot as plt
import xgboost as xgb
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

# 数据集的加载和划分
iris = load_iris()
X, y = iris.data, iris.target
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.8, random_state=3)

# 将数据处理成数据集格式DMatrix格式
dm_train = xgb.DMatrix(X_train, y_train)
dm_test = xgb.DMatrix(X_test)

# 设置模型参数
params = {
    'booster': 'gbtree',           # 用于训练的基学习器类型
    'objective': 'multi:softmax',  # 指定模型的损失函数
    'num_class': 3,                # 类别的数量
    'gamma': 0.1,                  # 控制每次分裂的最小损失函数减少量
    'max_depth': 6,                # 决策树最大深度
    'lambda': 2,                   # L2正则化权重
    'subsample': 0.8,              # 控制每棵树训练时随机选取的样本比例
    'colsample_bytree': 0.8,       # 用于控制每棵树或每个节点的特征选择比例
    'eta': 0.001,                  # 学习率
    'seed': 10,                    # 设置随机数生成器的种子
    'nthread': 16,                 # 指定了训练时并行使用的线程数
}

# 训练模型
model = xgb.train(params, dm_train, num_boost_round=200)
# 预测结果
y_pred = model.predict(dm_test)

# 输出模型评估报告
print(classification_report(y_test, y_pred))

# 绘制特征重要性评分
xgb.plot_importance(model)
plt.grid(False)
plt.show()
```

**`param`​模型参数字典：**

- `booster`: 指定用于训练的基学习器类型, 默认为 `'gbtree'`​, 表示使用传统的决策树作为基学习器, 适用于大多数问题, 尤其涉及到非线性关系, 可选: 
  - `'gblinear'`: 使用线性回归或逻辑回归, 数据集较小或线性关系较强时可用
  - `'dart'`: 基于决策树的模型, 但具有丢弃树的机制, 降低过拟合风险, 适用于复杂数据集, 尤其是在出现过拟合时
- `objective`: 指定模型的损失函数(优化目标), 可选: 

  - `'reg:squarederror'`: 回归任务中的均方误差(MSE), 用于回归任务
  - `'reg:logistic'`: 回归任务中的逻辑回归, 通常用于二分类任务
  - `'binary:logistic'`: 二分类任务中的逻辑回归, 输出概率值
  - `'binary:logitraw'`: 二分类任务中的逻辑回归, 输出未经过Sigmoid处理的原始值
  - `'multi:softmax'`: 多分类任务, 输出为每个类别的最大概率
  - `'multi:softprob'`: 多分类任务, 输出为每个类别的概率分布
- `eta`​/`learning_rate`: XGBoost中的学习率, 默认为0.3, 推荐将初始值设为0.01到0.1
- `alpha`​和`lambda`: 前者控制L1正则化项(Lasso)的强度, 默认为`0`​, 后者控制L2正则化项(Ridge)的强度, 默认为`1`​
- `scale_pos_weight`: 用于处理类别不平衡问题, 尤其是二分类问题, 在类别严重不平衡的情况下, 通过调整这个参数来加大少数类的权重, 使得模型更关注少数类样本, 默认为1
- `gamma`: 用来控制每次分裂的最小损失函数减少量, 该参数控制树的生长, 大的`gamma`​会使得树更小, 减小过拟合的风险, 默认为0, 意味着模型不会受到分裂的限制, 树会尽可能深, 直到节点中没有足够的样本
- `num_class`: 用于多分类任务的参数, 表示类别的数量, 二分类任务无需设置
- `colsample_bytree`​ / `colsample_bylevel`​ / `colsample_bynode`: 控制在每棵树、每一层、每个节点上采样特征的比例, 用于控制模型的复杂度, 较小的值会增加模型的随机性从而防止过拟合, 较大的值则意味着每棵树使用更多的特征, 可能导致过拟合

  - `colsample_bytree`: 每棵树使用的特征比例(默认为1.0)
  - `colsample_bylevel`: 每一层使用的特征比例(默认为1.0)
  - `colsample_bynode`: 每个节点使用的特征比例(默认为1.0)

**`xgb.train()`​超参数：**

- `num_boost_round`: 树的训练轮数, 设置较小的`learning_rate`​并增加训练轮数可以提高模型的稳定性
- `early_stopping_rounds`: 用于实现早期停止机制, 当指定轮次的训练中验证集上的损失函数不再减少时, 训练会自动停止, 避免过拟合
- `feval`: 设置用户自定义的评估函数, 允许灵活地使用任何适合特定任务的评估指标, 需要注意的是评估函数有两个参数, 一个表示模型预测值(NumPy的`ndarray`​对象), 一个是训练数据(XGBoost的`DMatrix`​对象), 函数返回一个二元组`(name, value)`​, 其中`name`​是评估指标的名称, `value`​是指标的值
- `obj`: 设置用户自定义的目标函数, 目标函数用于计算每一步的梯度和二阶导数, 从而指导模型的优化过程
- `evals`: 用于指定一个或多个验证集, 其值是包含一个或多个`(data, label)`​元组的列表, 每个元组代表一个评估数据集, 数据集需要是`DMatrix`​对象, 在训练过程中, XGBoost会在每一轮迭代后评估验证集上的性能, 通常用于监控训练过程中的过拟合或调整超参数
- `eval_results`: 存储在训练过程中计算的所有评估结果, 通常传入一个字典
- `verbose_eval`: 控制训练过程中评估结果的输出频率, 设为一个整数表示多少轮迭代输出一次评估结果, 设为`True`​或`False`​表示每轮都输出或不输出任何评估结果
- `xgb_model`: 用于加载之前训练好的模型, 以便从中断点继续训练, 可以指定一个`xgb_model`​文件或者传入一个`Booster`​对象
- `callbacks`: 在训练过程中添加自定义的回调函数, 回调函数可以在每一轮迭代时提供额外的控制, 如自动停止训练、调整学习

## LightGBM

```python
import lightgbm as lgb
import numpy as np
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

# 加载和划分数据集
iris = load_iris()
X, y = iris.data, iris.target
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.8, random_state=3)

# 将数据转化为 LightGBM 的数据格式
train_data = lgb.Dataset(X_train, label=y_train)
test_data = lgb.Dataset(X_test, label=y_test, reference=train_data)

# 设置模型参数
params = {
    'objective': 'multiclass',   # 多分类问题
    'num_class': 3,              # 类别数量
    'metric': 'multi_logloss',   # 多分类对数损失函数
    'boosting_type': 'gbdt',     # 使用梯度提升树算法
    'num_leaves': 31,            # 叶子节点数
    'learning_rate': 0.05,       # 学习率
    'feature_fraction': 0.75,    # 每次训练时随机选择特征的比例
    'early_stopping_rounds': 10  # 连续多少论没有性能提升就停止迭代
}
# 模型训练
model = lgb.train(params=params, train_set=train_data, num_boost_round=200, valid_sets=[test_data])
# 模型预测
y_pred = model.predict(X_test, num_iteration=model.best_iteration)
# 将预测结果处理成标签
y_pred_max = np.argmax(y_pred, axis=1)

# 查看模型评估报告
print(classification_report(y_test, y_pred_max))
```

**`param`​参数字典**：

1. `objective`: 设置优化目标函数(损失函数), 可选: 
    - `'regression'`: 回归任务
    - `'binary'`: 二分类任务
    - `'multiclass'`: 多分类任务
    - `'multiclassova'`: 多分类任务, 使用一对多的策略
    - `'rank_xendcg'`​、`'lambdarank'`: 排名任务
2. `metric`: 评估模型性能的指标, 可选值有: 

    - `'l2'`​、`'mean_squared_error'`: 回归任务中的均方误差
    - `'binary_error'`: 二分类错误率
    - `'multi_logloss'`: 多分类对数损失
    - `'auc'`: 二分类任务中的AUC
    - `'precision'`​、`'recall'`​、`'f1'`: 精度、召回率、F1分数
3. `boosting_type`: 设置提升类型, 可选: 

    - `'gbdt'`: 传统的梯度提升树
    - `'dart'`: 具有随机丢弃树机制来防止过拟合的决策树
    - `'goss'`: 通过单边梯度抽样来加速训练
    - `'rf'`: 随机森林
4. `num_leaves`​/`max_depth`: 决策树的叶子节点数 / 决策树的最大深度, 控制树的复杂度
5. `lambda_l1`​/`lambda_l2`: L1和L2正则化参数, 用于控制模型的复杂度, 防止过拟合
6. `max_bin`:  用于分割连续特征(数据分箱)的最大箱子数
7. `feature_fraction`: 每次训练时随机选择特征的比例
8. `early_stopping_rounds`:  设置评估指标在连续多少轮迭代中没有改进时, 训练会提前停止



---



# 神经网络模型

`neural_network`​模块`MLPClassifier`​

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.neural_network import MLPClassifier
from sklearn.metrics import classification_report

# 加载和划分数据集
iris = load_iris()
X, y = iris.data, iris.target
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.8, random_state=3)

# 创建多层感知机分类器模型
model = MLPClassifier(
    solver='lbfgs',                  # 优化模型参数的求解器
    learning_rate='adaptive',        # 学习率的调节方式为自适应 
    activation='relu',               # 隐藏层中神经元的激活函数 
    hidden_layer_sizes=(32, 32, 32)  # 每一层神经元的数量
)
# 训练和预测
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

# 查看模型评估报告
print(classification_report(y_test, y_pred))
```

**`MLPClassifier()`​超参数：**

- `hidden_layer_sizes`: 指定神经网络中每一层的神经元数目, 元组类型, 默认为`(100, )`​, 表示只有一个包含100个神经元的隐藏层, 层数越多, 神经元数目越多, 模型的表示能力就越强, 但也存在过拟合风险
- `activation`: 隐藏层中神经元的激活函数, 决定了网络每一层的输出形态, 默认为`relu`​, 表示使用ReLU激活函数, 训练深度网络时的首选, 能缓解梯度消失问题, 速度较快, 可选: 

  - `'identity'`: 线性激活函数, $\small{f(x) = x}$, 不推荐使用
  - `'logistic'`​ / `'tanh'`​ / `'relu'`: Sigmoid / 双曲正切 / ReLU 激活函数
- `solver`: 用来优化模型参数的求解器(优化算法), 可选: 

  - `'lbfgs'`: 拟牛顿法, 计算复杂度较高, 对小数据集表现较好
  - `'sgd'`: 随机梯度下降, 适用于大规模数据集, 在训练时会随机选取小批量数据进行更新
  - `'adam'`: 自适应矩估计, 默认, 适用于大多数情况且计算效率较高
- `alpha`: L2正则化项, 控制神经网络的复杂度, 默认为`0.0001`​
- `batch_size`: 每次迭代所用的样本批量大小, 默认为`'auto'`​, 根据样本数量自动决定批量大小, 较小的批量会使训练更加不稳定, 但有助于避免陷入局部最优解, 较大的批量则可以加速训练, 但可能导致内存不足
- `learning_rate`: 学习率的调节方式, 影响模型在每次迭代时调整权重的步长, 默认为`'constant'`​, 表示学习率保持不变, 可选: 

  - `'invscaling'`: 表示学习率随着迭代次数的增加而逐步减小
  - `'adaptive'`: 表示学习率会根据当前梯度变化自动调整, 当梯度更新较小时学习率会增加, 当梯度较大时学习率会减小
- `learning_rate_init`: 初始学习率, 默认为`0.001`​
- `max_iter`: 最大迭代次数, 控制训练过程中优化算法的最大迭代次数, 默认为`200`​, 若达到最大迭代次数后模型仍未收敛, 训练将停止, 防止过长时间训练, 但过小的值可能会导致训练中止太早, 导致欠拟合
- `tol`: 训练过程中的优化容忍度, 决定了当目标函数变化小于该值时训练停止, 默认为`0.0001`​, 较小的`tol`​会导致更长的训练时间, 较大的`tol`​可能会提前停止训练

‍

---



# 自然语言处理

## 词袋模型

`feature_extraction.text`​模块`CountVectorizer`​类

```python
from sklearn.feature_extraction.text import CountVectorizer

# 文档列表
documents = [
    'I love programming.',
    'I love machine learning.',
    'I love apple.'
]

# 创建词袋模型
cv = CountVectorizer()
X = cv.fit_transform(documents)

# 输出词汇表和词频向量
print('词汇表:\n', cv.get_feature_names_out())
print('词频向量:\n', X.toarray())
```

中文: `jieba`​库进行中文分词处理将句子拆分为词, 再构造词频向量

```python
import jieba
from sklearn.feature_extraction.text import CountVectorizer

# 加载停用词
with open('停用词表.txt') as file_obj:
	stop_words_list = file_obj.read().split('\n')

# 文档列表
documents = [
    '我在四川大学读书',
    '四川大学是四川最好的大学',
    '大学校园里面有很多学生',
]

# 创建词袋模型并指定分词函数
cv = CountVectorizer(
    tokenizer=lambda x: jieba.lcut(x),
    token_pattern=None,
	stop_words=stop_words_list
)
X = cv.fit_transform(documents)

# 输出词汇表和词频向量
print('词汇表:\n', cv.get_feature_names_out())
print('词频向量:\n', X.toarray())
```

## 词向量

```python
import re

from datasets import load_dataset
from gensim.models import Word2Vec
from sklearn.metrics.pairwise import cosine_similarity

# 加载 IMDB 数据集
imdb = load_dataset('imdb')
# 直接将 50000 条评论用作语料
temp = [imdb['unsupervised'][i]['text'] for i in range(50000)]
# 用正则表达式对评论文本进行简单处理
corpus = [re.sub(r'[^\w\s]', '', x) for x in temp]

# 预处理语料库(英文分词)
sentences = [sentence.lower().split() for sentence in corpus]
# 训练 Word2Vec 模型
# sentences - 输入语料库(句子构成的列表, 每个句子是一个或多个单词的列表)
# vector_size - 词向量的维度(维度越高能够表示的信息越多)
# windows - 上下文窗口大小(模型在训练时使用的上下文单词的范围)
# min_count - 忽略频率低于此值的单词(过滤掉在语料库中出现次数较少的单词)
# workers - 训练时使用的 CPU 核心数量
# seed - 随机数种子(用于初始化模型的权重)
model = Word2Vec(sentences, vector_size=100, window=10, min_count=2, workers=4, seed=3)

# 通过模型获取 king 和 queen 的词向量
king_vec, queen_vec = model.wv['king'], model.wv['queen']

# 计算两个词向量的余弦相似度
cos_similarity = cosine_similarity([king_vec], [queen_vec])
print(f'king 和 queen 的余弦相似度: {cos_similarity[0, 0]:.2f}')

# 通过词向量进行推理(king - man + woman ≈ queen)
man_vec, woman_vec = model.wv['man'], model.wv['woman']
result_vec = king_vec - man_vec + woman_vec
# 查找与计算结果最相似的三个词
similar_words = model.wv.similar_by_vector(result_vec, topn=3)
print(f'跟 king - man + woman 最相似的词:\n {similar_words}')

# 查找与 dog 最相似的五个词
dog_similar_words = model.wv.most_similar('dog', topn=5)
print(f'跟 dog 最相似的词:\n {dog_similar_words}')
```

