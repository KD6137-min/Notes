# Markdown行内/间公式:

- 行内: 一对美元符包括, `$ a+b $`
- 行间: 两对美元符包括, 公式单独显示一行, 默认居中,`$$ a+b $$`

# 操作数

文本: **`\text{}`**

| 小写 | 大写 | LaTeX      | 小写 | 大写 | LaTeX      |
| ---- | ---- | ---------- | ---- | ---- | ---------- |
| α    | Α    | `\alpha`   | ν    | Ν    | `\nu`      |
| β    | Β    | `\beta`    | ξ    | Ξ    | `\xi`      |
| γ    | Γ    | `\gamma`   | ο    | Ο    | `\omicron` |
| δ    | Δ    | `\delta`   | π    | Π    | `\pi`      |
| ε    | Ε    | `\epsilon` | ρ    | Ρ    | `\rho`     |
| ζ    | Ζ    | `\zeta`    | σ, ς | Σ    | `\sigma`   |
| η    | Η    | `\eta`     | τ    | Τ    | `\tau`     |
| θ    | Θ    | `\theta`   | υ    | Υ    | `\upsilon` |
| ι    | Ι    | `\iota`    | φ    | Φ    | `\phi`     |
| κ    | Κ    | `\kappa`   | χ    | Χ    | `\chi`     |
| λ    | Λ    | `\lambda`  | ψ    | Ψ    | `\psi`     |
| μ    | Μ    | `\mu`      | ω    | Ω    | `\omega`   |

# 集合&逻辑运算

- 并`a \cup b`, 交`a \cap b`
- 真子集`a \subset b`, 子集`a \subseteq b`
- 空集: `\emptyset`
- 大括号: `\{ \}`
- 属于`\in`, 不属于`\notin`
- 存在`\exists`, 任意`\forall`
- 集合括号`\{ a, b, c \}`
- 因为`\because`，所以`\therefore`
- 箭头：
  - 大写为双线：双向`\Leftrightarrow`或`iff`、右箭头`\Rightarrow`、左箭头`\Leftarrow`、长双向`\Longleftrightarrow`
  - 小写为单线：双向`\leftrightarrow`

- $\$

# 标记符

- 角标: 上标`a^{b}`, 下标`a_{b}`, 汉字角标`a^\mbox{汉字}` / `a^\text{注释}`
- 标线: 上标线`\overline{a+b}`, 下标线`\underline{a+b}`
- 括号: 需与`\left`或`\right`配合自动伸缩
  - 圆括号: `\left( a+b \right)`
  - 方括号:`\left[ a+b \right]`
  - 花括号: `\left\{ a+b \right\}`, 注意有两个\
  - 绝对值: `\left| a+b \right|`
  - 范数: `\left\| a+b \right\|`
  - 尖括号: `\left\langle a+b\right\rangle`
- 重音: 尖角`\hat{x}`, 短横线`\bar{x}`，大波浪线`\sim`, 上波浪线`\tilde`
- 省略号: 注意复数加s
  - 基线: `x_{1}, x_{2}, \ldots, x_{n}`
  - 居中: `x_{1}, x_{2}, \cdots, x_{n}`
  - 垂直: `\vdots`
  - 对角线: `\ddots`
- 空格: `a \quad b`

# 运算符

- 加减: `a \pm b`
- 乘: 点形式`a \cdot b`, 叉形式`a \times b`
- 除: 分数形式`\frac {a}{b}`, 除法形式`a \div b`
- 不等式: 小于等于`a \leq b`、大于等于`a \geq b`、不等于`a \neq b`、约等于`a \approx b`
- 根式: `\sqrt{ab}`, n次`\sqrt[n]{ab}`
- :bulb:对数: **`\log_{a}{b}`**, `\ln a, \lg a`
- 累乘: `\prod^{10}_{n=1} a_{n}`
- 累加: `\sum^{100}_{n=1} n`

# 微积分

- 无穷: `\infty`
- 积分: `\int^{b}_{a} {f(x)}dx`
- 极限: `\lim_{x \to {n}}`
- 梯度`\nabla`, 正三角`\triangle`

# 线性代数

- 向量: `\vec{v}`, 上左箭头`\overleftarrow {AB}`, 上右箭头`\overrightarrow {AB}`

- 矩阵: 开启矩阵环境

  ```latex
  \begin{bmatrix}
  1 & 2 & \cdots \\
  6 & 9 & \cdots \\
  \vdots & \vdots & \ddots \\
  \end{bmatrix}
  ```

  - `&`分隔列, `\\`分隔行
  - `{matrix}`无括号, `{bmatrix}`方括号, `{vmatrix}`直线括号（行列式）, `{pmatrix}`圆括号

# 多行公式与编号

多行公式主要使用以下环境：

| 环境       | 用途                                                   | 特点                           |
| ---------- | ------------------------------------------------------ | ------------------------------ |
| `equation` | 单个公式，有编号                                       | 常与 `aligned` 或 `split` 配合 |
| `align`    | 多行多公式，每行独立编号                               | 自动按 `&` 对齐                |
| `aligned`  | 嵌入到 `equation` 或其他环境中，使多行公式共享一个编号 | 不独立产生编号                 |
| `split`    | 类似 `aligned`，但更简洁，用于一个公式拆多行           | 不独立产生编号                 |
| `cases`    | 分段函数                                               | 自动添加左花括号               |

## `align` 环境

```latex
\begin{align}
f(x) &= (x+1)^2 \\
     &= x^2 + 2x + 1
\end{align}
```

多行多公式，`&` 用来对齐，通常放在等号前，**每行自动编号**，若某行不需要编号，用 `\nonumber`

## `equation + aligned`

```latex
\begin{equation}
\begin{aligned}
f(x) &= (x+1)^2 \\
     &= x^2 + 2x + 1
\end{aligned}
\end{equation}
```

整个 `equation` **只有一个编号**，常用于分段函数或推导的连续步骤

## `equation + split`

**单公式换行**，与 `aligned` 类似，但语法更简洁，专注于拆分一个长公式

```latex
\begin{equation}
\begin{split}
f(x) &= (x+1)^2 \\
     &= x^2 + 2x + 1
\end{split}
\end{equation}
```

## `cases` 环境

分段函数，自动生成大花括号和对齐格式，每行用 `\\` 分隔，右边用 `&` 分隔条件

```latex
f(x) = \begin{cases}
x^2, & x \ge 0 \\
-x,  & x < 0
\end{cases}
```

## `subequations` 环境

同编号不同字母，给多行公式添加相同主编号：如 (1a), (1b)

```latex
\begin{subequations}
\begin{align}
f(x) &= x^2 \\
g(x) &= x^3
\end{align}
\end{subequations}
```

