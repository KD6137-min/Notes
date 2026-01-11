# LaTeX

`LaTeX = TeX引擎 + 宏命令 + 环境 + 包`，本质为一门“宏语言 + 排版系统”

## 基本结构

- 前导区：只能写设置和元信息，如加载包、定义命令、改版式
- 正文区：`\begin{document}`开始，`\end{document}`结束

```latex
% 前导区
\documentclass{article}		% 顶级声明
\usepackage{amsmath} 		% 使用包
\newcommand{\R}{\mathbb{R}} % 宏定义

% 正文区
\begin{document}		% 切换环境，正文区
Hello, world!			% 正文
\end{document}
```

### 顶级声明

```latex
\documentclass[options]{class}
```

声明文档类型，文档入口，决定**语法行为、排版规则、整体风格与默认行为**

| class      | 用途                 |
| ---------- | -------------------- |
| `article`  | 论文、报告           |
| `report`   | 长报告（有 chapter） |
| `book`     | 书籍                 |
| `beamer`   | PPT                  |
| `IEEEtran` | IEEE 论文            |
| `ctexart`  | 中文论文             |

`[options]`为类的参数，影响class内部定义的宏行为

```latex
\documentclass[
  12pt,			% 字号
  a4paper,		% 纸张大小
  twocolumn		% 双栏
]{article}
```

### 元信息

标题、作者、日期，写在前导区，只定义信息，**不显示**

在正文区使用`\maketitle`生成标题页，真正显示标题、作者、日期

```latex
\documentclass{article}

% 只定义，不显示
\title{My First Paper}
\author{KD6_137}
\date{\today}

\begin{document}
\maketitle			% 真正显示
正文
\end{document}
```

- `\title{}`，副标题换行只用`\\`

- `\author{}`，多作者时只用`\and`，作者+单位时中间只用`\\`分隔，多作者+多单位时：

  ```latex
  \author{
  Zhang San$^{1} \and
  Li Si$^{2}\\[0.5em]
  $^{1}$ School,University\\
  $^{2}$ School,University
  }
  ```

- `\date{}`，`\date{\today}`自动填充，`\date{January 4,2026}`固定日期，`\date{}`空白则不显示日期

- `\maketitle`：一篇文档**只用一次**，必须放在`\begin{document}`之后，正文的最前面



## 常用语法

### 1. 注释

- `%`单行注释

- 条件法多行注释：

  ```latex
  \iffalse
  	中间内容不会被编译，可写错误代码，如\begin{错误}
  \fi
  ```

- `verbatim`宏

  ```latex
  % 需在前导区导包
  \usepackage{verbatim}
  
  \begin{document}
  ...
  
  \begin{comment}
  	注释，不能嵌套使用，不能用于注释复杂环境内部
  \end{comment}
  ```


### 2. 命令

基本形式：`\`开头，`[]`可选参数，`{}`必选参数

```latex
\command
\command{argument}
\command[optional]{argument}
```

### 3. 环境

基本形式：

```latex
\begin{env}
...
\end{env}
```

本质为作用域 + 状态切换

### 4. 包

`包 = 一组宏 + 环境 + 默认参数`

基本形式：

```latex
\usepackage{amsmath}
```

常用`amamath`数学公式、`graphicx`插图、`geometry`页面边距、`biblatex`参考文献

### 5. 列表

列表必须写在section里面，不要用`\\`强行换行，`\item`后直接写内容

- 无序列表：常用于贡献点、要点、条件说明等

  ```latex
  \begin{itemize}
    \item Low glycemic response
    \item Improved metabolic health
    \item High industrial applicability
  \end{itemize}
  ```

- 有序列表：常用于步骤、算法流程等，自动编号，不用手动编号

  ```latex
  \begin{enumerate}
    \item Data collection
    \item Feature extraction
    \item Model training
  \end{enumerate}
  ```

### 6. 公式

| 环境           | 用途                                     | 特点                           |
| -------------- | ---------------------------------------- | ------------------------------ |
| `equation`     | 单行，有编号                             | 常与 `aligned` 或 `split` 配合 |
| `align`        | 多行多公式，每行独立编号                 | 自动按 `&` 对齐                |
| `aligned`      | 嵌入到其他环境中，使多行公式共享一个编号 | 不独立产生编号                 |
| `split`        | 一个公式编号，多行展示（因为太长）       | 不独立产生编号                 |
| `cases`        | 分段函数                                 | 自动添加左花括号               |
| `subequations` | 一组公式共用大编号                       |                                |

行间公式：`$...$`包含，不要用`$$...$$`，不要手写编号，不要在公式外乱加空行

- 无编号公式：摘要中常用

  ```latex
  \[
  y = ax + b
  \]
  ```

- `equation`：单行公式，自动编号

  ```latex
  \begin{equation}
  y = ax + b
  \end{equation}
  ```

- `align`：多行公式，`&`为对齐点，每行一个`\\`，**每行自动编号**，若某行不需要编号，用 `\nonumber`

  ```latex
  \begin{align}
  y &= ax + b \\
  z &= cx + d
  \end{align}
  ```

- `cases`：分段函数，必须在**数学环境**中使用，左边为表达式，右边为条件，自动生成大花括号和对齐格式

  ```latex
  \begin{equation}
  f(x) =
  \begin{cases}
  x^2, & x \ge 0 \\
  -x,  & x < 0
  \end{cases}
  \end{equation}
  ```

- 拆行展示：只有一个编号，必须在equation环境中

  ```latex
  \begin{equation}
  \begin{split}
  L &= \sum_{i=1}^{N} (y_i - \hat{y}_i)^2 \\
    &\quad + \lambda \|\theta\|^2
  \end{split}
  \end{equation}
  ```

- `aligned`，不是公式环境，而是**对齐模块**，用于嵌套

  - 嵌在equation：一个编号，多行对齐，常用于分段函数或推导的连续步骤

    ```latex
    \begin{equation}
    \begin{aligned}
    y &= ax + b \\
    z &= cx + d
    \end{aligned}
    \end{equation}
    ```

  - 嵌在正文公式中：

    ```latex
    \[
    \begin{aligned}
    y &= ax + b \\
    z &= cx + d
    \end{aligned}
    \]
    ```

  | 对比       | align    | aligned  |
  | ---------- | -------- | -------- |
  | 能否单独用 | ✅        | ❌        |
  | 默认编号   | 每行一个 | 外部决定 |
  | 使用场景   | 主公式   | 公式组件 |

- `subequations`：一组公式属于同一定义/模型，使用较少，同一组主编号相同，字母不同，如 (1a), (1b)

  ```latex
  \begin{subequations}
  \begin{align}
  y &= ax + b \\
  z &= cx + d
  \end{align}
  \end{subequations}
  ```

- 在正文中引用公式：`~`为不换行空格（非常重要）

  ```latex
  As shown in Equation~\ref{eq:linear},
  ```

  ```latex
  \begin{equation}
  y = ax + b
  \label{eq:linear}
  \end{equation}
  ```

### 7. 图

图片放到`.tex`文件同目录或`figures/`文件夹

```latex
\begin{figure}[htbp] 	% [htbp]放的位置，由LaTeX决定
  \centering		% 居中
  \includegraphics[width=0.7\textwidth]{figures/result.png} 	% width=0.7\textwidth图宽
  \caption{picture's name}		% 图名
  \label{fig:result}		% 引用
\end{figure}
```

注意图片文件名、caption、label的替换

正文中引用图：`As shown in Fig.~\ref{fig:result}, ...`

### 8. 表格

#### 三线表

`\usepackage{booktabs}`，无竖线和行分隔线，不需要`\hline`堆叠，不推荐在同一行同时用线和加粗

顶线`\toprule`、中线`\midrule`、底线`\bottomrule`

```latex
\begin{table}[htbp]
\centering
\caption{Performance comparison on test dataset}
\label{tab:performance}
\begin{tabular}{lcc}
\toprule
Method & Accuracy (\%) & F1-score \\
\midrule
Baseline & 85.3 & 0.81 \\
Ours     & 89.7 & 0.87 \\
\bottomrule
\end{tabular}
\end{table}
```

`\begin{table}...\end{table}`为浮动体，负责编号、caption、引用

`\begin{tabular}...\end{tabular}`真正画表格

`{lcc}`列格式：`l`为左对齐，`c`为居中，`r`为右对齐

`\caption`永远在tabular外，且表caption在上，图caption在下

正文引用表格：`As shown in Table~\ref{tab:performance},...`

### 9. 参考文献

两套系统：

- `BibTeX`（旧）：简单，行为固定，审稿系统偏好，CVPR/ICCV/ECCV等顶会投稿必须用`BibTeX`
- `biblatex`（新）：规则更清晰、可控性更强、对中文/多格式友好，除非强制用`BibTeX`，否则均推荐用`biblatex`，学位论文/国内期刊/自由写作推荐用

> 期刊要看模板

#### BibTeX

> [!IMPORTANT]
>
> 绝对原则：BibTeX 是**模板的一部分**，稳定、保守、模板驱动，不能选风格、换宏包、引入 biblatex，只能**配合模板**

```latex
% 输出文献表，位置固定在全文最后，格式固定，不能动
\bibliographystyle{ieee_fullname}
\bibliography{egbib}	% 这里egbib换成自己的.bib文件的前缀名
```

- `.bib`文件：唯一手写的地方，一个纯文本的**文献数据库**，真正管理文献的地方，只维护该文件即可实现不用手动调文献顺序、不用手动改编号、不复制粘贴作者名

  ```tex
  % 标准论文条目模板
  @inproceedings{he2016resnet,	
    author    = {He, Kaiming and Zhang, Xiangyu and Ren, Shaoqing and Sun, Jian},
    title     = {Deep Residual Learning for Image Recognition},	% 标题
    booktitle = {Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},		% 会议全名
    year      = {2016}
  }
  ```

  - 永远不要手写`.bib`，要用官方来源
  - 文献格式统一、作者名完整不缩写、会议名规范
  - 多作者：必须是`姓，名 and 姓，名`，`author = {He, Kaiming and Zhang, Xiangyu}`
  - 防止被改大小写：`title = {{CNN} for Image Classification}`，凡是缩写一律加`{}`
  - 除非明确支持url，否则能不写就不写，`url = {...}`
  - 原则上不引用中文文献，若必须则需转成英文或放在补充材料

- `\cite{key}`：正文命令，`As shown in~\cite{he2016resnet}, ...`

  - 基本型（数值）：效果为`[3]`

    ```latex
    \cite{he2016resnet}
    ```

  - 多篇文献：效果为`[3,4]`，不要拆成多个`\cite`

    ```latex
    \cite{he2016resnet,krizhevsky2012imagenet}
    ```

#### biblatex

自己掌控参考文献规则

- 前导区：加载 + 配置

  ```latex
  \usepackage[
    backend=biber,	% 不能改
    style=numeric,
    sorting=nyt		
  ]{biblatex}
  
  \addbibresource{references.bib}
  ```

  - 3种style：`numeric`理工科推荐、`authoryear`社科、`alphabetic`少数期刊
  - 3种sorting，`nyt`：name、year、title，`nty`：name、title、year，`none`：按引用顺序

- 正文引用：

  - 基础版：`\cite{he2016resnet}`
  - 作者+年：`\textcite{he2016resnet}`，效果`He et al. (2016)`
  - 括号作者年：`\parencite{he2016resnet}`，效果`(He et al., 2016)`
  - 页码、章节：`\cite[Sec.~3]{he2016resnet}`，写综述用

- 输出文献表：`\printbibliography`

- `.bib`：`@article`期刊、`@inproceedings`会议、`@book`书、`@online`网站（biblatex专属）

> [!caution]
>
> - 用biber编译
> - 不可同时加载`natbib`
> - style乱换
> - 大小写要用`{}`保护

|          | BibTeX      | biblatex   |
| -------- | ----------- | ---------- |
| 后端     | bibtex      | **biber**  |
| 风格控制 | `.bst` 文件 | **宏参数** |
| 中英文   | 痛苦        | **友好**   |
| 可定制性 | 低          | **极高**   |
| 推荐度   | 模板强制    | 模板允许时 |



## 文章结构

### 摘要

切换`abstract`环境，摘要中不能再分节，只能是一整块文字，不要用空行分段，可以但不推荐写公式/引用，默认不缩进，不编号，不用做改变

```latex
\begin{document}
\maketitle

% 必须放在\maketitle之后
\begin{abstract}
摘要...
\end{abstract}

% 摘要后面直接接正文
\section{Introduction}
正文...
\end{document}
```

### 正文结构

```latex
\section{一级标题}
\subsection{二级标题}
\subsubsection{三级标题}
```

不能跳级，自动排版，**不需要写编号**

section下面必须有内容，至少写一句话

空行=新段落

标题中不推荐写公式

致谢、附录前说明等无需编号场景时，使用`\section*{Acknowledgments}`，此处`*`表示显示标题，但不编号、不进目录



## 词法

### 操作数

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

### 集合&逻辑运算

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

### 标记符

- 角标: 上标`a^{b}`, 下标`a_{b}`, 汉字角标`a^\mbox{汉字}` / `a^\text{注释}`

- 标线: 上标线`\overline{a+b}`, 下标线`\underline{a+b}`

- 转置：`a^top`更规范，或`a^T`

- 括号: 需与`\left`或`\right`配合自动伸缩
  - 圆括号: `\left( a+b \right)`
  - 方括号:`\left[ a+b \right]`
  - 花括号: `\left\{ a+b \right\}`, 注意有两个\
  - 绝对值: `\left| a+b \right|`
  - 范数: `\lVert a+b \rVert_n`，需要在导言区使用`\usepackage{amsmath}`
  - 尖括号: `\left\langle a+b\right\rangle`
  - 有括号嵌套时外层用`\big(`和`\big)`
  
- 重音: 尖角`\hat{x}`, 短横线`\bar{x}`，大波浪线`\sim`, 上波浪线`\tilde`

- 省略号: 注意复数加s
  - 基线: `x_{1}, x_{2}, \ldots, x_{n}`
  - 居中: `x_{1}, x_{2}, \cdots, x_{n}`
  - 垂直: `\vdots`
  - 对角线: `\ddots`
  
- 空格:

  | 代码     | 效果     | 说明          |
  | -------- | -------- | ------------- |
  | `\,`     | 小空格   | 约为 1/6 quad |
  | `\ `     | 普通空格 | 最常用        |
  | `\quad`  | 较大空格 | 一个 quad     |
  | `\qquad` | 特大空格 | 两个 quad     |

### 运算符

- 加减: `\pm`
- 乘: 点形式`\cdot`, 叉形式`\times`，逐元素乘积`\odot`
- 除: 分数形式`\frac {a}{b}`, 除法形式`a \div b`
- 不等式: 小于等于`a \leq b`、大于等于`a \geq b`、不等于`a \neq b`、约等于`a \approx b`
- 根式: `\sqrt{ab}`, n次`\sqrt[n]{ab}`
- :bulb:对数: **`\log_{a}{b}`**, `\ln a, \lg a`
- 累乘: `\prod^{10}_{n=1} a_{n}`
- 累加: `\sum^{100}_{n=1} n`

### 字体

三套系统：正文字体、数学字体、结构字体（基于正文）

`\documentclass{article}`默认字体：Computer Modern（正文）、Computer Modern Math（数学）

除非期刊要求，一般不改动字体，如要求Times New Roman：`\usepackage{newtxtext,newtxmath}`，必须**成对使用**，不能只换正文，不要使用`\usepackage{times}`（过时）

| 字体命令            | 意义                                                   |
| ------------------- | ------------------------------------------------------ |
| `\textbf{重要结论}` | 粗体，视觉强调（慎用），永远是粗体，不管上下文         |
| `\textit{et al.}`   | 斜体，拉丁词                                           |
| `\texttt{code}`     | 代码 / 文件名                                          |
| `\emph{emphasis}`   | 语义强调（推荐），根据上下文字体自动选择合适的强调方式 |

> `\text{}`不是普通文本命令，只在数学模式中用于插入正常文字

#### 数学字体

数学字体是数学语义的一部分，标量、向量等有特殊数学约定的字体，不可乱用

公式中出现英文单词、条件说明、单位、说明性文字，均要`\text{}`包含，否则会被当成字母变量乘法处理

- `\bm{}`：推荐，可智能地加粗几乎所有数学符号，且会保留原有的斜体属性，被认为是排版数学粗体的正确方式
  
  - 需在文档导言区（`\documentclass`之后，`\begin{document}`之前）加入 `\usepackage{bm}`
  
- 其他字体：

  | 字体命令             | 效果示例                   | 主要用途说明                                            |
  | -------------------- | -------------------------- | ------------------------------------------------------- |
  | `\mathnormal`或 默认 | $\mathnormal{A}$(斜体)     | 数学公式中变量的默认字体，符合标准规范                  |
  | `\mathrm`            | $\mathrm{A}$(直立)         | 用于排版直立字符，如常数单位、sin等正体符号             |
  | `\mathit`            | $\mathit{A}$(文本斜体)     | 用于表示多字母的变量名，如 `\mathit{Var}`               |
  | `\mathbf`            | $\mathbf{A}$(粗体直立)     | **常用于表示向量、矩阵**                                |
  | `\mathsf`            | $\mathsf{A}$(无衬线直立)   | 使用无衬线字体，适用于特定排版风格                      |
  | `\mathtt`            | $\mathtt{A}$(等宽打字机体) | 模拟打字机效果，有时用于表示代码                        |
  | `\mathcal`           | $\mathcal{A}$(手写体)      | **常用于表示损失函数、集合或特殊算子**                  |
  | `\mathbb`            | $\mathbb{A}$(黑板粗体)     | **专用于表示数集**（如实数域），通常需要 `amsfonts`宏包 |
  | `\mathfrak`          | $\mathfrak{A}$(哥特体)     | 用于表示李代数等特殊数学对象，通常需要 `amssymb`宏包    |


### 微积分

- 无穷: `\infty`
- 积分: `\int^{b}_{a} {f(x)}dx`
- 极限: `\lim_{x \to {n}}`
- 梯度`\nabla`, 正三角`\triangle`，变化量`\Delta`，偏导`\part`

### 线性代数

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



