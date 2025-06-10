# Markdown行内/间公式:

- 行内: 一对美元符包括, `$ a+b $`
- 行间: 两对美元符包括, 公式单独显示一行, 默认居中,`$$ a+b $$`

# 操作数

- 希腊字母
  - α -- Α -- `\alpha`
  - β -- Β -- `\beta`
  - γ -- Γ -- `\gamma`
  - δ -- Δ -- `\delta`
  - ε -- Ε -- `\epsilon`
  - ζ -- Ζ -- `\zeta`
  - η -- Η -- `\eta`
  - θ -- Θ -- `\theta`
  - ι -- Ι -- `\iota`
  - κ -- Κ -- `\kappa`
  - λ -- Λ -- `\lambda`
  - μ -- Μ -- `\mu`
  - ν -- Ν -- `\nu`
  - ξ -- Ξ -- `\xi`
  - ο -- Ο -- `\omicron`
  - π -- Π -- `\pi`
  - ρ -- Ρ -- `\rho`
  - σ, ς -- Σ -- `\sigma`
  - τ -- Τ -- `\tau`
  - υ -- Υ -- `\upsilon`
  - φ -- Φ -- `\phi`
  - χ -- Χ -- `\chi`
  - ψ -- Ψ -- `\psi`
  - ω -- Ω -- `\omega`

- 文本: `\test` x = 0

# 逻辑运算

- 并`a \cup b`, 交`a \cap b`
- 真子集`a \subset b`, 子集`a \subseteq b`
- 空集: `\emptyset`
- 大括号: `\{ \}`
- 属于`\in`, 不属于`\notin`
- 存在`\exists`, 任意`\forall`

# 标记符

- 角标: 上标`a^{b}`, 下标`a_{b}`, 汉字角标`a^\mbox{汉字}`
- 标线: 上标线`\overline {a+b}`, 下标线`\underline {a+b}`
- 括号: `\left`或`\right+括号`
  - 圆括号: `\left( a+b \right)`
  - 方括号:`\left[ a+b \right]`
  - 花括号: `\left\{ a+b \right\}`, 注意有两个\
  - 绝对值: `\left| a+b \right|`
  - 范数: `\left\| a+b \right\|`
  - 尖括号: `\left\langle a+b\right\rangle`
- 重音: 尖角`\hat {x}`, 短横线`\bar {x}`
- 波浪线: 大波浪线`\sim`, 上波浪线`\tilde`
- 三圆点: 注意复试加s
  - 位于基线: `x_{1}, x_{2}, \ldots, x_{n}`
  - 居中: `x_{1}, x_{2}, \cdots, x_{n}`
  - 垂直: `\vdots`
  - 对角线: `\ddots`
- 空格: `a \quad b`

# 运算符

- 加减: `a \pm b`
- 乘: 点形式`a \cdot b`, 叉形式`a \times b`
- 除: 分数形式`\frac {a}{b}`, 除法形式`a \div b`
- 不等式: 小于等于`a \leq b`、大于等于`a \geq b`、不等于`a \neq b`、约等于`a \approx b`
- 根式: `\sqrt {ab}`, n次`\sqrt [n]{ab}`
- 对数: `\log_{a}{b}`, `\ln{a}, \lg{a}`
- 累乘: `\prod^{10}_{n=1} a_{n}`
- 累加: `\sum^{100}_{n=1} n`

# 微积分

- 无穷: `\infty`
- 积分: `\int^{b}_{a} {f(x)}`
- 极限: `\lim_{x \to {n}}`
- 梯度倒三角`\nabla`, 正三角`\triangle`

# 线性代数

- 向量: `\vec a`, 上左箭头`\overleftarrow {AB}`, 上右箭头`\overrightarrow {AB}`

- 矩阵: 开启矩阵环境

  ```latex
  \begin{matrix}
  1 & 2 & \cdots \\
  6 & 9 & \cdots \\
  \vdots & \vdots & \ddots \\
  \end{matrix}
  ```

  - `&`分隔列, `\\`分隔行
  - `{matrix}`无括号, `{bmatrix}`方括号, `{vmatrix}`直线括号, `{pmatrix}`圆括号

# 文本格式

- equation环境: 自动生成序号, 换行符`\\`需配合`aligned`换行

  ```latex
  \begin{equation}
  f(x)= \left\{
  \begin{aligned}
  x &= \cos(t) \\
  y &= \sin(t) \\
  \end{aligned}
  \right.
  \end{equation}
  
  \begin{equation}
  \begin{aligned}
  f(x) &= (x + 1)^2 \\
  	 &= x^2 + 2x + 1. \nonumber 
  \end{aligned}
  \end{equation}
  ```

  - 使用`\nonumber`标记不需要编号的行

- align环境: 支持换行符`\\`, 适合输入多行公式, 不同于`aligned`环境

  ```latex
  \begin{align}
  f(x) &= (x + 1)^2 \\
   	 &= x^2 + 2x + 1.
  \end{align}
  ```

- aligned环境: 常放在equation环境中使用, 表示多行公式只占用当前equation环境的一个编号

  ```latex
  # 用于分段函数
  f(x)= \left\{ 
  \begin{aligned}
  x &= \cos(t) \\
  y &= \sin(t) \\
  \end{aligned}
  \right.
  ```

  - 注意`\right`后为`.`而不是`}`
  - 使用`&=`可对齐等式

- split环境: 适用于将一个公式拆分成多行

  ```latex
  \begin{equation}
  \begin{split}
  f(x) &= (x + 1)^2 \\
  	 &= x^2 + 2x + 1.
  \end{split}
  \end{equation}
  ```

- subequations环境: 使用相同的主编号给子公式编号, 如`1a`和`1b`