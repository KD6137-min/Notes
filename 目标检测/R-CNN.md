# R-CNN

**在 R-CNN 之前**：

- 目标检测基本依赖 **手工特征**（HOG、DPM、SIFT 等）
- 检测流程：先生成候选框（region proposals），再用 SVM + 特征描述符分类，效果有限
- ImageNet 2012 之后，AlexNet 展示了 CNN 在分类任务的巨大潜力

**R-CNN 的问题意识**：CNN 在分类上很强，能不能把它迁移到检测任务？



**用深度 CNN 替代手工特征的第一代检测器，开启了深度学习目标检测的时代，但速度太慢，只是过渡方案**



## 创新点

1. **引入 CNN 特征到检测**
    - 这是第一次把 CNN（卷积神经网络）大规模应用到目标检测里，替代了传统的 HOG/DPM 特征
    - 利用**预训练的 AlexNet** 提取图像区域特征，再做分类
2. 提出**Region Proposal + CNN 特征** 思路：
    - 用 **Selective Search** 先产生 ~2000 个候选区域（region proposals）
    - 把每个区域裁剪、缩放到固定大小
    - 送入 CNN 提取特征，再用 SVM 分类 + 回归器调整边框
3. **迁移学习**
    - 使用 ImageNet 分类预训练的 CNN，然后在检测数据集（PASCAL VOC）上微调
    - 这是深度学习时代目标检测**迁移学习的开端**

总结：**R-CNN = proposals + CNN 特征 + SVM 分类 + 边框回归**

## 缺点

1. **速度极慢**：2000 proposals × 每个都过 CNN → 推理极慢（一个图像几十秒）
2. **存储开销大**：要保存每个 region 的 CNN 特征（几百 GB）
3. **不是真正的 end-to-end**：检测部分和分类部分分离，CNN 训练 → SVM 训练 → 边框回归器训练 → 分开优化

## 影响

1. **“Region-based” 框架**
    - R-CNN → Fast R-CNN → Faster R-CNN → Mask R-CNN，构成了 **two-stage 检测器的主线**
    - 核心思路：先产生候选框，再 refine → 至今仍在高精度场景里使用（比如自动驾驶、医学影像）
2. **迁移学习与 fine-tuning**
    - “用分类网络做 backbone” 变成了目标检测的常规做法
    - 直到今天，ResNet/ViT 等也都是先在大数据集上预训练，再迁移到检测
3. **CNN 特征替代传统特征**：从 R-CNN 开始，CNN 正式取代 HOG、DPM，深度学习成为检测主流

## 论文结构

1. **引言**：
    - 目标检测的难点（背景复杂、类间差异小）
    - 动机：CNN 在分类成功，能否用于检测？
2. **方法（R-CNN 框架）**
    - Selective Search 提 proposals
    - CNN 提特征
    - SVM 分类
    - 边框回归
3. **实验**
    - 在 PASCAL VOC 上实验，和传统 DPM 对比
    - 消融实验（proposal 数量、fine-tuning 的作用）
4. **结果与讨论**
    - 大幅度提升了 mAP
    - 分析 CNN 特征 vs 传统特征的差异
5. **结论**
    - CNN 特征在检测上比传统方法更强
    - 未来方向：更快的 proposal、端到端