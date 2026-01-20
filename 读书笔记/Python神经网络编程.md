# Python神经网络编程

# 基本原理

## 线性分类器

![截屏2025-01-11 16.24.29](../assets/截屏2025-01-11%2016.24.29-20250111162431-xfnkbcv.png)

$$
E = (\Delta A)x
$$

$$
\Delta A = E / x
$$

适度改进 moderate, 小心谨慎地向训练样本方向移动, 保持先前训练迭代周期中所得值的一部分

学习率L: 调节改进速率, 使单一的训练样本不能主导整个学习过程

$$
\Delta A = L (E / x)
$$

可使用多个现行分类器来划分由单一直线无法分离的数据

## 阶跃函数

‍

![截屏2025-01-11 16.31.04](../assets/截屏2025-01-11%2016.31.04-20250111163105-fj305br.png)

![截屏2025-01-11 16.31.17](../assets/截屏2025-01-11%2016.31.17-20250111163119-h4u9r67.png)

改进阶跃函数: S函数, 又称逻辑函数, 平滑, 自然, 接近现实

‍

![截屏2025-01-11 16.32.02](../assets/截屏2025-01-11%2016.32.02-20250111163204-6bnqvst.png)

$$
y = \frac {1}{1 + e^{-x}}
$$

![截屏2025-01-11 16.39.32](../assets/截屏2025-01-11%2016.39.32-20250111163934-wdxcm7e.png)

## 构建多层神经元

![截屏2025-01-11 16.46.14](../assets/截屏2025-01-11%2016.46.14-20250111164616-ih0uhzj.png)

调整这些连接的权重即可, 学习过程中优化权重得到越来越好的结果

‍

以两层为例: 

![截屏2025-01-11 16.51.17](../assets/截屏2025-01-11%2016.51.17-20250111165119-syawe45.png)![截屏2025-01-11 16.52.50](../assets/截屏2025-01-11%2016.52.50-20250111165252-233g174.png)![截屏2025-01-11 16.55.08](../assets/截屏2025-01-11%2016.55.08-20250111165510-l7tjlww.png)

多层、多节点的神经网络, 可用<u>矩阵计算</u>方便地进行权重计算

![截屏2025-01-11 17.01.40](../assets/截屏2025-01-11%2017.01.40-20250111170142-xfkzrrm.png)​

$$
X = W \cdot I
$$

W为权重矩阵

I为输入矩阵

X为组合调节后的信号

‍

来自第二层的最终输出为

$$
O = sigmoid(X)
$$

以3层9节点为例: ![截屏2025-01-11 17.13.04](../assets/截屏2025-01-11%2017.13.04-20250111171305-v3htovl.png)

输入: 

$$
I = 
\begin{pmatrix} 
0.9\\ 
0.1 \\ 
0.8  
\end{pmatrix}
$$

第一层/输入层(input)与中间层(hidden)的权重: 

$$
W_{input\_hidden} = 
\begin{pmatrix} 
0.9&0.3&0.4\\
0.2&0.8&0.2\\
0.1&0.5&0.6
\end{pmatrix}
$$

$$
X_{hidden} = W_{input\_hidden} \cdot I = \begin{pmatrix} 1.16\\0.42\\0.62 \end{pmatrix}
$$

中间层的输出: 

$$
O_{hidden} = sigmoid(X_{hidden}) = \begin{pmatrix} 0.761\\0.603\\0.650\end{pmatrix}
$$

中间层与输出层(output)的权重:

$$
W_{hidden\_output} = \begin{pmatrix}
0.3&0.7&0.5\\
0.6&0.5&0.2\\
0.8&0.1&0.9
\end{pmatrix}
$$

$$
X_{output} = W_{hidden\_output} \cdot O_{hidden} = \begin{pmatrix} 0.975\\0.888\\1.254 \end{pmatrix}
$$

![截屏2025-01-11 17.55.33](../assets/截屏2025-01-11%2017.55.33-20250111175535-b1qogwb.png)

输出层输出(最终输出): 

$$
O_{output} = \begin{pmatrix} 0.726\\ 0.708\\ 0.778 \end{pmatrix}
$$

## 反向传播误差

用最终输出得到的误差值调整: 多输出节点<u>**反向传播误差**</u>

![截屏2025-01-11 17.59.03](../assets/截屏2025-01-11%2017.59.03-20250111175905-niuvjto.png)​

1. 等分误差
2. 不等分误差: 权重大的分配更多误差

![截屏2025-01-11 18.02.27](../assets/截屏2025-01-11%2018.02.27-20250111180229-nniic51.png)

将e1和e2<u>按比例</u>分割来更新W

比例计算: 

$$
\frac{W_{1,1}}{W_{1,1} + W_{2,1}}
$$

![截屏2025-01-11 18.08.22](../assets/截屏2025-01-11%2018.08.22-20250111180824-bweaxl9.png)

中间层节点的误差为其所有反向传播的误差的和: 

![截屏2025-01-11 18.11.02](../assets/截屏2025-01-11%2018.11.02-20250111181104-nxd7qbv.png)

$$
\begin{aligned}
e_{hidden,1} &= \text{链接} W_{1,1} \text{和链接} W_{1,2} \text{上的分割误差之和} \\
&= e_{output,1} \times \frac{W_{1,1}}{W_{1,1} + W_{2,1}} + e_{output,2} \times \frac{W_{1,2}}{W_{1,2} + W_{2,2}}
\end{aligned}
$$

![截屏2025-01-11 18.37.33](../assets/截屏2025-01-11%2018.37.33-20250111183735-z63i0pe.png)![截屏2025-01-11 18.37.50](../assets/截屏2025-01-11%2018.37.50-20250111183754-dlvvzzu.png)

利用矩阵乘法进行反向传播误差

$$
error_{output} = \begin{pmatrix} e_1 \\e_2 \end{pmatrix}
$$

$$
error_{hidden} =
\begin{pmatrix}
\frac{w_{1,1}}{w_{1,1} + w_{2,1}} & \frac{w_{1,2}}{w_{1,2} + w_{2,2}} \\
\frac{w_{2,1}}{w_{2,1} + w_{1,1}} & \frac{w_{2,2}}{w_{2,2} + w_{1,2}}
\end{pmatrix}
\cdot
\begin{pmatrix}
e_1 \\
e_2
\end{pmatrix}
$$

太复杂, <u>考虑简化</u>: 若忽略分母, 仅失去后馈误差的大小, 仍可做到权重大的分得更多误差, 大体不差(实践证明有效)

简化后: 

$$
\begin{aligned}
error_{hidden} &= 
\begin{pmatrix}
W_{1,1}&W_{1,2}\\
W_{2,1}&W_{2,2}
\end{pmatrix}
\cdot
\begin{pmatrix}
e_1\\
e_2
\end{pmatrix}\\
&= W^{T}_{hidden\_output} \cdot error_{output}
\end{aligned}
$$

## 梯度下降法

如何调整权重: 组合太多, 暴力求解不可取

实际做法: 梯度下降, 不必理会复杂的<u>误差函数</u>, 用步进方式接近答案

示例: 希望找到x, 使y最小

![截屏2025-01-11 19.54.14](../assets/截屏2025-01-11%2019.54.14-20250111195416-v7tih6s.png)![截屏2025-01-11 20.00.06](../assets/截屏2025-01-11%2020.00.06-20250111200008-8azte02.png)

向斜率为负的方向步进

改变步子大小以避免超调, 防止在最小值附近左右横跳

调节步长, 与梯度大小成比例

基于假设: 在接近最小值时斜率变得平缓

![截屏2025-01-11 20.07.23](../assets/截屏2025-01-11%2020.07.23-20250111200725-8uxyg3o.png)![截屏2025-01-11 20.08.39](../assets/截屏2025-01-11%2020.08.39-20250111200841-l08fjdv.png)

可能卡在错误的山谷(不是最低的山谷)

方法: 从不同的起点开始(即不同的起始链接权重), 多次训练网络

‍

误差函数的选择: 并不使用(目标值-输出值), 一般使用差的平方, 更容易计算梯度下降的斜率, 更平滑连续, 没有间断和跳跃, 越接近最小值梯度越小, 超调风险小

‍

一个链接权重: 

![截屏2025-01-11 20.32.19](../assets/截屏2025-01-11%2020.32.19-20250111203225-g0ydn9p.png)

两个链接权重: 

![截屏2025-01-11 20.32.54](../assets/截屏2025-01-11%2020.32.54-20250111203256-b87n2xc.png)

总结: 只需关注一个值$\frac {\delta E}{\delta W_{j,k}}$, 即误差函数的斜率

展开E: 

$$
\frac{\delta E}{\delta W_{j,k}} = \frac{\delta}{W_{j,k}} \cdot \Sigma_n (t_n - o_n)^2
$$

又, 误差函数不需要对所有输出节点求和, 因为节点的输出只取决于所连接的链接, 即取决于链接权重x, 上式可简化为

$$
\frac{\delta E}{\delta W_{j,k}}  = \frac{\delta}{\delta W_{j,k}} (t_k - o_k)^2
$$

‍

又根据链式法则: 

$$
\begin{aligned}
\frac{\delta E}{\delta W_{j,k}} &= \frac{\delta E}{\delta o_k} \cdot \frac{\delta o_k}{\delta W_{j,k}}\\&=-2(t_k-o_k) \cdot \frac{\delta}{\delta W_{j,k}} sigmoid(\Sigma_j W_{j,k} \cdot o_j)
\end{aligned}
$$

又根据: 

$$
\frac{\delta}{\delta x} sigmoid(x) = sigmoid(x)(1-sigmoid(x))
$$

故: 

$$
\begin{aligned}
\frac{\delta E}{\delta W_{j,k}} &= -2(t_k-o_k) \cdot sigmoid(\Sigma_j W_{j,k} \cdot o_j)(1-sigmoid(\Sigma_j W_{j,k} \cdot o_j)) \cdot \frac{\delta}{\delta W_{j,k}}(\Sigma_jW_{j,k} \cdot o_j)\\
&=-2(t_k-o_k) \cdot sigmoid(\Sigma_j W_{j,k} \cdot o_j)(1-sigmoid(\Sigma_j W_{j,k} \cdot o_j)) \cdot o_j\\
&=-(e_k) \cdot sigmoid(\Sigma_j W_{j,k} \cdot o_j)(1-sigmoid(\Sigma_j W_{j,k} \cdot o_j)) \cdot o_j
\end{aligned}
$$

我们只关注误差函数的斜率, 因此可略去2

学习率$\alpha$  

因此新权重: 斜率取反

$$
new \quad W_{j,k} = old \quad W_{j,k} - \alpha \cdot \frac{\delta E}{\delta W_{j,k}}
$$

忽略学习率的权重变化矩阵为: 

$$
\begin{pmatrix}
\Delta W_{1,1}&\Delta W_{2,1}&\Delta W_{3,1}&...\\
\Delta W_{1,2}&\Delta W_{2,2}&\Delta W_{3,2}&...\\
\Delta W_{1,3}&\Delta W_{2,3}&\Delta W_{j,k}&...\\
\quad ...&\quad ...&\quad ...&...
\end{pmatrix}
 = \begin{pmatrix}
E_1 \cdot S_1(1-S_1)\\
E_2 \cdot S_2(1-S_3)\\
E_k \cdot S_k(1-S_k)\\
...
\end{pmatrix}
\cdot \begin{pmatrix}
o_1&o_2&o_j&...
\end{pmatrix}
$$

第一项使用下一层(即节点K)的值, 第二项使用前一层(节点J)的值

故: 

$$
\Delta W_{j,k} = \alpha \cdot E_k \cdot O_k(1-O_k) \cdot O_j^T
$$

‍

## 注意

![截屏2025-01-12 13.29.00](../assets/截屏2025-01-12%2013.29.00-20250112132903-eh23vnz.png)

- 调整输入值: 

  - 平坦的激活函数的问题: 权重的改变取决于激活函数的梯度, 小梯度会限制神经网络的学习, 即饱和神经网络

    - 解决: 尽量保持小的输入
  - 权重改变值也取决于输入信号$o_j$, 输入过小或过大会导致丧失精度

    - 解决: 不可让输入信号太小, 范围控制在0～1之间, 若输入0, 则加上一个小偏移如0.01, 避免表达式等于0造成的学习能力的丧失(同理也要避开1, 如0.999)
- 调整输出值: 避免激活函数不可能到达的值, 避开1和0
- 随机初始权重: 避免过大的初始权重

  - 历史经验: 可在一个节点传入**<u>链接数量平方根倒数</u>**的范围内随机采样, 初始化权重
  - 禁止将初始权重设定为0

‍

# Python实现

代码框架: 

```python
# 神经网络类
class NeuralNetwork:
    def __init__(self):
        pass

    # 训练神经网络
    def train(self):
        pass

    # 查询神经网络
    def query(self):
        pass
```

完整: 

```python
import numpy as np
import scipy.special as spc
import matplotlib.pyplot as plt
%matplotlib inline

# 神经网络类
class NeuralNetwork:
    # 初始化神经网络
    def __init__(self, inputnodes, hiddennodes, outputnodes, learningrate):
        self.inodes = inputnodes
        self.hnodes = hiddennodes
        self.onodes = outputnodes
        # 学习率
        self.lr = learningrate

        # 初始权重矩阵, 二选一
        # 简单、流行: 创建两个链接权重矩阵, 范围-0.5～0.5
        # self.wih = (np.random.rand(self.hnodes, self.inodes) - 0.5)
        # self.who = (np.random.rand(self.onodes, self.hnodes) - 0.5)

        # 复杂: 正太概率分布采样权重
        self.wih = np.random.normal(0.0, pow(self.hnodes, -0.5), (self.hnodes, self.inodes))
        self.who = np.random.normal(0.0, pow(self.onodes, -0.5), (self.onodes, self.hnodes))

        # 使用的激活函数, expit函数即为sigmoid函数
        self.activation_function = lambda x: spc.expit(x)
        # 反向查询, 使用expit函数的逆
        self.inverse_activation_function = lambda x: spc.logit(x)

    # 训练神经网络
    def train(self, target_list, inputs_list):
        # 将输入列表转为2d数组
        inputs = np.array(inputs_list, ndmin=2).T
        # 目标输出矩阵, 用于计算误差改进权重
        targets = np.array(target_list, ndmin=2).T

        # 中间层
        # 输入信号
        hidden_inputs = np.dot(self.wih, inputs)
        # 输出信号
        hidden_outputs = self.activation_function(hidden_inputs)

        # 最终输出层
        # 输入信号
        final_inputs = np.dot(self.who, hidden_outputs)
        # 输出信号
        final_outputs = self.activation_function(final_inputs)

        # 误差矩阵, target - actual
        output_errors = targets - final_outputs
        # 反向传播误差
        hidden_errors = np.dot(self.who.T, output_errors)
        # 更新权重
        self.who += self.lr * np.dot(
            (output_errors * final_outputs * (1 - final_outputs)),
            np.transpose(hidden_outputs)
        )
        self.wih += self.lr * np.dot(
            (hidden_errors * hidden_outputs * (1 - hidden_outputs)),
            np.transpose(inputs)
        )

    # 查询神经网络, 接收输入, 返回网络的输出
    def query(self, inputs_list):
        # 将输入列表转为2d数组
        inputs = np.array(inputs_list, ndmin=2).T

        # 中间层
        # 输入信号
        hidden_inputs = np.dot(self.wih, inputs)
        # 输出信号
        hidden_outputs = self.activation_function(hidden_inputs)

        # 最终输出层
        # 输入信号
        final_inputs = np.dot(self.who, hidden_outputs)
        # 输出信号
        final_outputs = self.activation_function(final_inputs)

        return final_outputs

    # 反向查询
    def backquery(self, target_list):
        final_outputs = np.array(target_list, ndmin=2).T

        # 计算最终输出层的输入信号
        final_inputs = self.inverse_activation_function(final_outputs)
        # 计算中间层的输出信号
        hidden_outputs = np.dot(self.who.T, final_inputs)
        # 标准化中间层的输出, 范围0.01～0.99
        hidden_outputs -= np.min(hidden_outputs)
        hidden_outputs /= np.max(hidden_outputs)
        hidden_outputs *= 0.98
        hidden_outputs += 0.01

        # 计算中间层的输入信号
        hidden_inputs = self.inverse_activation_function(hidden_outputs)
        # 计算输入层的输出信号
        inputs = np.dot(self.wih.T, hidden_inputs)
        # 标准化输入层的输出, 范围0.01～0.99
        inputs -= np.min(inputs)
        inputs /= np.max(inputs)
        inputs *= 0.98
        inputs += 0.01

        return inputs
```

训练网络: 

```python
# 创建实例
input_nodes = 784
hidden_nodes = 200
output_nodes = 10
learning_rate = 0.1
n = NeuralNetwork(input_nodes, hidden_nodes, output_nodes, learning_rate)

# 多世代训练网络
with open('mnist_test_60000.csv', 'r') as f:
    training_data_list = f.readlines()
epochs = 10
for e in range(epochs):
    for record in training_data_list:
		# 每一行中, 第一个数字为标签, 即目标值, 其他784为构成图像像素的颜色值, 范围0～255
        all_values = record.split(',')
		# 标准化输入, 范围0.01～1.00
        inputs = (np.asfarray(all_values[1:]) / 255.0 * 0.99) + 0.01
		# 构建目标矩阵(10个元素的数组)
        targets = np.zeros(output_nodes) + 0.01
	    # 10个输出层节点, 只有一个节点被激发, 其余保持抑制状态
        targets[int(all_values[0])] = 0.99
        n.train(targets, inputs)

        # 数据增强
        # 旋转正10度
        inputs_plus10_img = ndimage.rotate(
            inputs.reshape(28, 28),
            # 旋转角度
            10,
            # 填充值
            cval=0.01,
            order=1,
            reshape=False
        )
        n.train(targets, inputs_plus10_img.reshape(784))
        # 旋转负10度
        inputs_minus10_img = ndimage.rotate(
            inputs.reshape(28, 28),
            -10,
            cval=0.01,
            order=1,
            reshape=False
        )
        n.train(targets, inputs_minus10_img.reshape(784))
```

测试网络: 

```python
with open('mnist_test_10.csv', 'r') as f:
    test_data_list = f.readlines()
# 计分卡
scorecard = []

for record in test_data_list:
    all_values = record.split(',')
    correct_label = int(all_values[0])
    # 标准化输入
    inputs = (np.asfarray(all_values[1:]) / 255.0 * 0.99) + 0.01
    outputs = n.query(inputs)
    # np.argmax()返回最大值的索引
    label = np.argmax(outputs)
    if label == correct_label:
        scorecard.append(1)
    else:
        scorecard.append(0)

scorecard_array = np.asfarray(scorecard)
print('performance = ', scorecard_array.sum() / scorecard_array.size)
```

向后查询: 

```python
# 向后查询, 给定标签, 看输出什么
label = 0
# 构建目标数组
targets = np.zeros(output_nodes) + 0.01
targets[label] = 0.99
image_data = n.backquery(targets)
plt.imshow(image_data.reshape(28, 28), cmap='Greys', interpolation='None')
```

## 一些改进

- 不断调整学习率: 学习率存在**甜蜜点**, 过大或过小都有损性能
- 多世代训练: 训练次数也有**甜蜜点**, 过多也有损性能(过拟合)
- 改变网络形状: 隐藏层是学习发生的场所, 隐藏层节点前后的链接权重具有学习能力, 隐藏节点即为**学习容量**, 太多时, 太多路径可供学习选择, 难以训练网络, 太少则学习能力差
- 数据增强: 对训练数据旋转、缩放、反转等, 让网络学到不同的模式

‍
