# mamba

- 新型序列建模架构，基于 **状态空间模型（State Space Models，SSM）**

- 核心公式：输入序列被处理成一个隐状态 `h(t)`，这个隐状态用一个 **线性动态系统** 更新，同时通过一个 **选择性机制（Selective）** 让模型根据输入动态改变权值（类似注意力），与 Transformer 的 key-query-value 注意力不同，**Mamba 完全没有 self-attention**

- 结果：时间复杂度从 **O(L²)** 变为 **O(L)**（线性），序列越长越占优（比如长文本、长视频）

- 各项指标极强：

  - 性能：多项任务上 **媲美甚至超越 Transformer**，比 LLM 常用的注意力更稳定、更便宜

  - 速度：序列处理速度通常比 Transformer 快 **2～20 倍**，显存开销更小，长序列轻松跑

常用模型：

- VMamba（2024）：将 2D 特征图转换成 4 条扫描序列（水平、垂直等），每条序列用 Mamba block，再 reshape 回 2D
- Detection-Mamba（专为检测设计）：Mamba 替代 Transformer encoder，用 selective SSM 建模全局上下文，对高分辨率遥感检测效果非常好（提升 3–5 AP）
- YOLO 系方法的 Mamba 替换模块：**YOLOv8-Mamba**、**YOLOv9-Mamba**、**RT-DETR-Mamba**、**Deformable DETR + Mamba**、**Anchor-free Mamba 结构（比如代替 RepBlock、C2f）**

