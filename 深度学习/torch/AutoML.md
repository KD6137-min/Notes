# AutoML

**Automated Machine Learning**，**自动化机器学习**，自动完成机器学习流程中繁琐、技术门槛高的步骤

|步骤|AutoML 的作用|
| ------------| ---------------------------------------------|
|特征工程|自动选择或生成最有用的特征|
|模型选择|自动尝试多种模型（如决策树、SVM、神经网络）|
|超参数调优|自动搜索最佳超参数组合（如学习率、深度）|
|模型评估|自动选择表现最好的模型|
|集成学习|自动组合多个模型来提升性能|
|数据预处理|如填充缺失值、标准化等|

```python
automl.fit(X_train, y_train)
pred = automl.predict(X_test)
```

常见 AutoML 工具：

|名称|语言|特点|
| ---------------| ------------| --------------------------------------|
|Auto-sklearn|Python|基于 scikit-learn，适合 tabular 数据|
|TPOT|Python|基于遗传算法自动组合 pipeline|
|H2O AutoML|多语言支持|工业级自动建模工具|
|Google AutoML|云服务|支持图像、文本等自动建模|
|AutoKeras|Python|专注深度学习模型搜索（NAS）|



---



# AutoGluon

**自动机器学习（AutoML）工具包**，深度学习框架接口，支持多种数据类型的任务，旨在让**机器学习变得更简单、更自动化**

`Gluon`​，译为“胶子”，把夸克粘在一起的粒子，意为把模型粘合在一起

**主要模块**：

|**模块名**|**说明**|**典型任务示例**|
| ------| --------------------------------------| -------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`autogluon.tabular`​|表格数据建模（结构化数据）|分类、回归、时间序列、推荐、自动特征工程、模型融合，支持LGBM、XGBoost、CatBoost、NN|
|​`autogluon.text`​|文本数据建模，使用 NLP 模型|文本分类、情感分析、新闻分组、句子回归、命名实体识别，支持Transformer，自动模型选择和优化，支持GPU加速训练|
|​`autogluon.vision`​|图像数据建模，基于图像分类框架|图像分类（自动裁剪、增强）、人脸识别、医学图像分析等，使用CNN，支持多GPU/自动调参|
|​`autogluon.multimodal`​|多模态建模（结合文本、图像、表格等）|产品描述图+文字分类、OCR 等复合任务，用统一接口处理多模态任务，支持OpenCV图像、文本描述、结构化字段联合建模|
|​`autogluon.timeseries`​|时间序列预测|销量预测、传感器信号分析、时序趋势预测，多变量时序输入，支持缺失值处理，支持DeepAR、ETS、Autoformer等算法，提供rolling forecast、训练窗口自动选择能力|

## tabular

译为表格型的，通常指 `.csv`​、Excel、SQL 表、Pandas DataFrame 这样的格式，每一行是一个样本，每一列是一个特征或标签

```python
from autogluon.tabular import TabularPredictor

predictor = TabularPredictor(label=label).fit(
    train_data.drop(columns=[id]),
    hyperparameters='multimodal',
    num_stack_levels=1,
    num_bag_folds=5
)

preds = predictor.predict(test_data)
```

|参数名|含义|
| --------| ------------------------------------------------------------------------------|
|​`label=...`​|指定目标列|
|​`.drop(columns)`​|删除不参与训练的列（如 id）|
|​`hyperparameters='multimodal'`​|使用多模态建模能力（适配文本/图像/结构化数据）|
|​`num_stack_levels=1`​|使用一层模型堆叠（提升效果），每一层模型以上一层模型的预测为输入|
|​`num_bag_folds=5`​|使用 5 折交叉验证 + bagging 提高泛化能力，每折模型稍有不同，最终平均预测结果|

主数据为结构化数据，且数据中混合了文本、图像等字段，可使用 `TabularPredictor`​，通过参数`hyperparameters='multimodal'`​ 激活多模态能力，统一了接口，使得用户理解成本低，在内部，AutoGluon 会自动使用 `autogluon.multimodal`​ 里的深度学习模型进行嵌入和融合，但对用户屏蔽了这些细节

### autogluon中的stack和bag协同工作

eg：`num_bag_folds=5, num_stack_levels=2`​

第一层每种模型都会训练 5 个子模型（不同折），第二层输入是第一层的平均预测结果（跨折）

第二层也会使用 Bagging，每个组合器模型也有 5 个子版本

可以叠加出非常复杂、强大的模型堆叠结构，且自动管理数据泄露问题（stack的输入是跨验证预测，只用验证集输出作为下一层输入，防止label泄露）

## multimodal

只在**非结构化任务**中直接使用 `autogluon.multimodal`​：

|使用场景|推荐模块|
| -------------------------------------| ----------|
|一张完整的表格数据|✅`TabularPredictor`​|
|图像分类任务（图像+label）|✅`ImagePredictor`​|
|文本分类任务（文本+label）|✅`TextPredictor`​|
|非表格形式的多模态任务（比如图+文）|✅`MultiModalPredictor`​|

# H2O

成熟、高性能的**开源自动机器学习（AutoML）平台和框架**，支持自动建模、分布式计算，目标是让**大规模机器学习更快、更易用、更自动化**，适用于大规模表格数据建模和企业级应用场景及对模型自动化、可解释性和稳定性有较高需求的用户

功能：

- 自动完成特征选择、模型训练、模型融合（Stacking/Ensemble）
- 支持分类、回归、多类别任务
- 支持模型导出（MOJO/POJO）
- 自带模型解释（SHAP、Partial Dependence 等）

支持的模型：

- **GBM**（Gradient Boosting Machine）
- **XGBoost**
- **GLM**（广义线性模型）
- **Deep Learning**（多层感知器）
- **Random Forest**
- **Stacked Ensembles**

使用示例：

```python
import h2o
from h2o.automl import H2OAutoML

h2o.init()

# 加载数据
data = h2o.import_file("your_dataset.csv")
train, test = data.split_frame(ratios=[.8])
x = data.columns[:-1]
y = data.columns[-1]

# 自动训练模型
aml = H2OAutoML(max_models=10, seed=1)
aml.train(x=x, y=y, training_frame=train)

# 查看结果
lb = aml.leaderboard
print(lb)

# 预测
preds = aml.leader.predict(test)
```

和 AutoGluon 的区别：

|对比项|H2O AutoML|AutoGluon|
| -------------| ---------------------------------| ------------------------------------|
|编程语言|Java（底层）+ Python 接口|Python|
|分布式训练|支持（天然分布式）|单机为主（支持多 GPU）|
|模型种类|传统 ML + 神经网络|传统 ML + 深度学习（包含 BERT 等）|
|多模态支持|有限，主要是表格数据|强（表格+文本+图像）|
|易用性|很强，支持 GUI（Driverless AI）|编程为主，API 简单|
|开源/商业版|有商业版（Driverless AI）|完全开源|



# NAS

神经网络架构搜索，通过强化学习、进化算法、梯度优化等方法，**自动搜索最优的网络结构**（如层类型、连接方式、宽度深度等）

- **DARTS**：使用梯度下降优化架构参数
- **EfficientNet**：通过复合缩放（深度、宽度、分辨率）自动平衡模型效率与精度
