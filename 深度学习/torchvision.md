# torchvision

PyTorch的官方扩展库，专为计算机视觉任务设计，提供了预训练模型（如 ResNet、VGG 等）、数据集加载工具（如 ImageNet、CIFAR）和数据增强方法（如 transforms）

# `torchvision.datasets`​

**包含数据集**：MNIST、COCO、LSUN Classification、ImageFolder、Imagenet-12、CIFAR10 and CIFAR100、STL10，可通过类直接调用：

```python
from torchvision import datasets
# MNIST示例
dataset = datasets.MNIST(
    root='./data',          # 数据集存储路径
    train=True,             # 加载训练集（False为测试集）
    download=True,          # 自动下载数据集
    transform=transforms.ToTensor()  # 图像预处理
)
```

**关键参数**：

- `root`: 数据集存储路径
- `train`​：True为训练集，False为测试集
- `download`​：从网上下载数据，并放在root目录下
- `transform`: 图像预处理（如 `transforms.Compose([...])`​）
- `target_transform`: 标签的转换函数

## `.ImageFolder()`​

`.ImageFolder()`​专门用于加载图像分类数据集，自动从文件夹结构中加载图像数据（每个子文件夹代表一个类别）

- 特点：自动生成标签（基于文件夹名）、内置支持数据预处理（如 `transforms`​）、适用于标准分类数据集（如 ImageNet 结构）
- **数据组织方式：目录结构**：

  ```plaintext
  root/
      class_0/  # 文件夹名即类别名
          img1.jpg
          img2.jpg
      class_1/
          img1.jpg
          ...
  ```
- **自动标签**：文件夹名映射为整数标签（如 `{"cat": 0, "dog": 1}`​）
- **预处理与转换：** 直接通过 `transform`​ 参数应用预处理
- 示例：

  ```python
  transform = transforms.Compose([
      transforms.Resize(256),
      transforms.ToTensor(),
      transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225])
  ])
  dataset = ImageFolder(root="path/to/data", transform=transform)
  ```
- 若数据符合文件夹分类结构，优先使用 `ImageFolder`​

## `.CocoCaptions()`​

`.CocoCaptions()`​用于加载 **MS COCO Caption 数据集**

- 包含约 33 万张图像，每张图像由人工标注至少 5 句自然语言描述
- 需指定**图像目录和标注文件路径**加载数据
- **返回格式**：每张图像返回 `(image, captions)`​，`image`​ 为 PIL Image 对象，`captions`​ 是包含多个描述语句的列表（如 `["A black motorcycle parked...", ...]`​）

```python
from torchvision.datasets import CocoCaptions
import matplotlib.pyplot as plt

# 加载训练集
dataset = CocoCaptions(
    root="path/to/train2017", 
    annFile="path/to/captions_train2017.json"
)

# 显示图像和描述
image, captions = dataset[0]
plt.imshow(image)
plt.axis('off')
for i, caption in enumerate(captions):
    plt.text(0, 10 + i*15, f"{i+1}: {caption}", color='white', fontsize=8)
plt.show()
```

# `torchvision.models`​

包含AlexNet、VGG、ResNet、SqueezeNet、DenseNet模型结构

```python
import torchvision.models as models
alexnet = models.alexnet()
vgg11 = models.vgg11()
resnet18 = models.resnet18()
squeezenet = models.squeezenet1_0()
densenet = models.densenet_161()
```

提供预训练模型：

- 旧版本：PyTorch 1.10前，**通过** **​`pretrained`​**​ **参数加载，**​`model = models.resnet18(pretrained=True)`​自动下载并加载预训练权重

  - **底层机制**：`pretrained=True`​ 会触发 `torch.utils.model_zoo`​ 从 PyTorch 官方服务器下载对应的权重文件（如 `resnet18-5c106cde.pth`​），并通过 `load_state_dict`​ 加载到模型中

    - `torch.utils.model_zoo`​ ：底层用于下载和管理模型权重的工具，用户通常无需直接调用
- 新版本：推荐使用`weights`​参数加载，`model = models.resnet18(weights=ResNet18_Weights.IMAGENET1K_V1)`​，支持多版本权重（如 `IMAGENET1K_V1/V2`​），权重来源和版本控制更灵活，且兼容本地缓存和自定义权重路径

|AlexNet|VGG|ResNet|SqueezeNet|DenseNet|
| :----------------------: | --------------------| --------| ------------| ----------|
|​`.alexnet()`​|​`.vgg11()`​、`.vgg11_bn()`​|​`.resnet18()`​|​`.squeezenet1_0()`​|​`.densenet121()`​|
||​`.vgg13()`​、`.vgg13_bn()`​|​`.resnet34()`​|​`.squeezenet1_1()`​|​`.densenet169()`​|
||​`.vgg16()`​、`.vgg16_bn()`​|​`.renset50()`​||​`.densenet201()`​|
||​`.vgg19()`​、`.vgg_bn()`​|​`.resnet101()`​||​`.densenet161()`​|
|||​`.resnet152()`​|||

`_bn`​表示with batch normalization

# `torchvision.transforms`​

支持常见的计算机视觉转换，可用于对不同任务（图像分类、检测、分割、视频分类）的数据进行训练或推理的转换或增强

**两个版本：**

- v1：即`torchvision.transforms`​，轻量，适合简单的图像分类预处理
- v2：即`torchvision.transforms.v2`​，是v1的超集，在v1基础上扩展和优化

  - 多任务支持：不仅支持图像分类任务，还新增了对目标检测、实例/语义分割以及视频任务的支持
  - **结构化输入**：`v2`​ 可以处理任意结构的输入（如单张图像、`(img, label)`​ 元组、嵌套字典等），而 `v1`​ 仅支持单张图像
  - **向后兼容性**：`v2`​ 的 `functional`​ API 保留了 `v1`​ 的核心功能（如 `Resize`​、`Crop`​ 等），但新增了对 `BoundingBox`​、`Mask`​ 等类型的支持
  - **模块化设计**：`v2`​ 的 `transforms`​ 继承自 `nn.Module`​，可直接嵌入 PyTorch 模型，而 `v1`​ 的 `transforms`​ 是独立函数
  - **内存效率**：`v2`​ 通过 `Tensor`​ 子类（如 `BoundingBoxes`​、`Mask`​）包装输入数据，减少内存拷贝
  - **动态调度**：`v2`​ 的 `functional`​ API 根据输入类型自动选择最优计算内核，如 `resize_image`​ vs. `resize_video`​
  - 支持更多转换，未来的改进仅添加到v2中

**支持的输入类型：**

- PIL图像
- 张量输入：推荐，支持CPU和CUDA张量，性能高，形状应为（C，H，W）或（N，C，H，W）接受任意数量的前导维度（....，C，H，W），推荐使用`torch.uint8`​的dtype

**两种形式：**

- 转换类：如`Resize`​
- 函数式操作：如`resize()`​，由`torchvision.transforms.v2.functional`​提供

**经典的转换流水线：**

```python
from torchvision.transforms import v2
transforms = v2.Compose([
    v2.ToImage(),  # Convert to tensor, only needed if you had a PIL image
    v2.ToDtype(torch.uint8, scale=True),  # optional, most input are already uint8 at this point
    # ...
    v2.RandomResizedCrop(size=(224, 224), antialias=True),  # Or Resize(antialias=True)
    # ...
    v2.ToDtype(torch.float32, scale=True),  # Normalize expects float input
    v2.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
])
```

> 注意ToTensor()默认会对图像类数据进行归一化，其他数据类型不会处理

**v2 API：**

- 组合：

  |转换类|功能|
  | --------| --------------------------------|
  |​`v2.Compose(transforms)`​​|将多个变换组合在一起|
  |​`v2.RandomApply(transforms[, p])`​​|以给定的概率随机应用一系列变换|
  |​`v2.RandomChoice(transforms[, p])`​​|从列表中随机选择并应用单个变换|
  |​`v2.RandomOrder(transforms)`​​|以随机顺序应用一系列变换|
- 几何转换：

  |转换类|功能|对应函数式操作|
  | -----------------------| --------------------------------------------------------------------------------| -------------------------------|
  |​`v2.Resize(size)`​|将输入调整为给定大小|​`v2.functional.resize(input, size)`​|
  |​`v2.ScaleJitter()`​|对输入执行大尺度抖动||
  |​`v2.RandomShortestSize(min_size)`​|随机调整输入大小||
  |​`v2.RandomResize(min_size, max_size)`​|随机调整输入大小||
  |​`v2.RandomCrop(size)`​|在随机位置裁剪输入|​`v2.functional.crop(inpt, top, left, height, ...)`​|
  |​`v2.RandomResizedCrop(size)`​|裁剪输入的随机部分并将其调整为给定大小|​`v2.functional.resized_crop(inpt, top, left, ...)`​|
  |​`v2.RandomIoUCrop()`​|随机IoU裁剪转换||
  |​`v2.CenterCrop(size)`​|在中心裁剪输入，size可为tuple或int（得到正方形）|​`v2.functional.center_crop(inpt, output_size)`​|
  |​`v2.FiveCrop(size)`​|将图像或视频裁剪为四个角和中心区域|​`v2.functional.five_crop(inpt, size)`​|
  |​`v2.TenCrop(size)`​|将图像或视频裁剪为四个角和中心区域，以及这些区域的翻转版本（默认使用水平翻转）|​`v2.functional.ten_crop(inpt, size[, ...])`​|
  |​`v2.RandomHorizontalFlip([p])`​|以给定概率水平翻转输入|​`v2.functional.horizontal_flip(inpt)`​|
  |​`v2.RandomVerticalFlip([p])`​|以给定概率垂直翻转输入|​`v2.functional.vertical_flip(inpt)`​|
  |​`v2.Pad(padding)`​|在输入所有侧面填充padding像素|​`v2.functional.pad(inpt, padding)`​|
  |​`v2.RandomZoomOut()`​|缩小（Zoom out）转换||
  |​`v2.RandomRotation(degrees)`​|按角度旋转输入|​`v2.functional.rotate(inpt, angle)`​|
  |​`v2.RandomAffine(degrees)`​|对输入进行随机仿射变换，保持中心不变|​`v2.functional.affine(inpt, angle, translate, ...)`​|
  |​`v2.RandomPerspective()`​|以给定概率对输入执行随机透视变换|​`v2.functional.perspective(inpt, startpoints, ...)`​|
  |​`v2.ElasticTransform()`​|使用弹性变换对输入进行转换|​`v2.functional.elastic(inpt, displacement)`​|
- 颜色转换：

  |转换类|功能|对应函数式操作|
  | -----------------------| ------------------------------------------------------------------------------| -------------------------------|
  |​`v2.ColorJitter()`​|随机更改图像或视频的亮度、对比度、饱和度和色调||
  |​`v2.RandomChannelPermutation()`​|随机置换图像或视频的通道||
  ||根据给定的排列方式对输入的通道进行重排|​`v2.functional.permute_channels(inpt, permutation)`​|
  |​`v2.RandomPhotometricDistort()`​|随机扭曲图像或视频||
  |​`v2.Grayscale()`​|将图像或视频转换为灰度图|​`v2.functional.rgb_to_grayscale(inpt)`​或`v2.functional.to_grayscale(inpt)`​|
  |​`v2.RGB()`​|将图像或视频转换为 RGB（如果它们不是 RGB）|​`v2.functional.grayscale_to_rgb(inpt)`​|
  |​`v2.RandomGrayscale([p])`​|以概率 p（默认 0.1）随机将图像或视频转换为灰度图|\`\`|
  |​`v2.GaussianBlur(kernel_size)`​|使用随机选择的高斯模糊核模糊图像|​`v2.functional.gaussian_blur(inpt, kernel_size)`​|
  |​`v2.GaussianNoise()`​|为图像或视频添加高斯噪声|​`v2.functional.gaussian_noise(inpt)`​|
  |​`v2.RandomInvert([p])`​|以给定概率反转给定图像或视频的颜色|​`v2.functional.invert(inpt)`​|
  |​`v2.RandomPosterize(bits[, p])`​|以给定概率通过减少每个颜色通道的位数来对图像或视频进行色调分离（Posterize）|​`v2.functional.posterize(inpt, bits)`​|
  |​`v2.RandomSolarize(threshold[, p])`​|以给定概率通过反转高于阈值的所有像素值来对图像或视频进行曝光过度（Solarize）|​`v2.functional.solarize(inpt, threshold)`​|
  |​`v2.RandomAdjustSharpness(sharpness_factor[, p])`​|以给定概率调整图像或视频的锐度|​`v2.functional.adjust_sharpness(inpt, ...)`​|
  |​`v2.RandomAutocontrast([p])`​|以给定概率对给定图像或视频的像素进行自动对比度调整（Autocontrast）|​`v2.functional.autocontrast(inpt)`​或`v2.functional.adjust_contrast(inpt, ...)`​|
  |​`v2.RandomEqualize([p])`​|以给定概率均衡给定图像或视频的直方图|​`v2.functional.equalize(inpt)`​|
  ||调整亮度|​`v2.functional.adjust_brightness(inpt, ...)`​|
  ||调整饱和度|​`v2.functional.adjust_saturation(inpt, ...)`​|
  ||调整色相|​`v2.functional.adjust_hue(inpt, hue_factor)`​|
  ||调整伽马值|​`v2.functional.adjust_gamma(inpt, gamma[, gain])`​|
- 其他：

  |转换类|功能|对应函数式操作|
  | -----------------------| ----------------------------------------------------------| -------------------------------|
  |​`v2.LinearTransformation(...)`​|使用离线计算的方阵变换矩阵和均值向量来变换张量图像或视频||
  |​`v2.Normalize(mean, std[, inplace])`​|使用均值和标准差对张量图像或视频进行归一化|​`v2.functional.normalize(inpt, mean, std[, ...])`​|
  |​`v2.RandomErasing([p, scale, ratio, value, ...])`​|在输入的图像或视频中随机选择一个矩形区域并擦除其像素|​`v2.functional.erase(inpt, i, j, h, w, v[, ...])`​|
  |​`v2.Lambda(lambd, *types)`​|将用户定义的函数作为变换应用||
  |​`v2.SanitizeBoundingBoxes([min_size, ...])`​|移除退化/无效的边界框及其对应的标签和掩码|​`v2.functional.sanitize_bounding_boxes(...[, ...])`​|
  |​`v2.ClampBoundingBoxes()`​|将边界框限制在对应的图像尺寸内|​`v2.functional.clamp_bounding_boxes(inpt[, ...])`​|
  |​`v2.UniformTemporalSubsample(num_samples)`​|从视频的时间维度中均匀采样 num\_samples 个索引|​`v2.functional.uniform_temporal_subsample(...)`​|
  |​`v2.JPEG(quality)`​|对给定的图像应用 JPEG 压缩和解压缩|​`v2.functional.jpeg(image, quality)`​|
- 转换：

  |转换类|功能|对应函数式操作|
  | --------| ----------------------------------------------------------------| ----------------|
  |​`v2.ToImage()`​|将张量、ndarray 或 PIL 图像转换为 Image，此操作不缩放值|​`v2.functional.to_image(inpt)`​|
  |​`v2.ToPureTensor()`​|将所有 TVTensors 转换为纯张量，移除相关元数据（如有）||
  |​`v2.PILToTensor()`​|将 PIL 图像转换为相同类型的张量，此操作不缩放值|​`v2.functional.pil_to_tensor(pic)`​|
  |​`v2.ToPILImage([mode])`​|将张量或 ndarray 转换为 PIL 图像，|​`v2.functional.to_pil_image(pic[, mode])`​|
  |​`v2.ToDtype(dtype[, scale])`​|将输入转换为特定的 dtype，可选地对图像或视频的值进行缩放|​`v2.functional.to_dtype(inpt[, dtype, scale])`​|
  |​`v2.ConvertBoundingBoxFormat(format)`​|将边界框坐标转换为给定的 format，例如从 "CXCYWH" 转换为 "XYXY"|​`v2.functional.convert_bounding_box_format(inpt)`​|
- 自动增强：

  |转换类|功能|
  | --------| ---------------------------------------------------------------------------------------|
  |​`v2.AutoAugment([policy, interpolation, fill])`​|基于 "AutoAugment: Learning Augmentation Strategies from Data"|
  |​`v2.RandAugment([num_ops, magnitude, ...])`​|基于 "RandAugment: Practical automated data augmentation with a reduced search space"|
  |​`v2.TrivialAugmentWide([num_magnitude_bins, ...])`​|基于 "TrivialAugment: Tuning-free Yet State-of-the-Art Data Augmentation"|
  |​`v2.AugMix([severity, mixture_width, ...])`​|基于 "AugMix: A Simple Data Processing Method to Improve Robustness and Uncertainty"|
- CutMix&MixUp：

  |转换类|功能|
  | --------| -----------------------------------|
  |​`v2.CutMix(*[, alpha, num_classes, labels_getter])`​|对提供的图像和标签批次应用 CutMix|
  |​`v2.MixUp(*[, alpha, num_classes, labels_getter])`​|对提供的图像和标签批次应用 MixUp|
- 开发者工具：

  |转换类|功能|
  | --------| ----------------------------------------------------------------------------|
  |​`v2.Transform()`​|实现自定义 v2 变换的基类|
  |​`v2.functional.register_kernel(functional, ...)`​|装饰一个 kernel 以便将其注册到 functional 和（自定义的）tv\_tensor 类型|
  |​`v2.query_size(flat_inputs)`​|返回高度和宽度|
  |​`v2.query_chw(flat_inputs)`​|返回通道数、高度和宽度|
  |​`v2.get_bounding_boxes(flat_inputs)`​|返回输入中的边界框|
  |​`v2.Lambda(lambda)`​|用于封装用户自定义的变换逻辑，性能低于转换类|
- 已弃用：

  |转换类|功能|对应函数式操作|
  | -----------------| -----------------| -----------------|
  ||||
  |​`v2.ToTensor()`​|请改用`v2.Compose([v2.ToImage(), v2.ToDtype(torch.float32, scale=True)])`​或`to_image() 、 to_dtype()`​|​`v2.functional.to_tensor(inpt)`​|
  |​`v2.ConvertImageDtype([dtype])`​|请改用`v2.ToDtype(dtype, scale=True)`​或`to_dtype()`​|​`v2.functional.convert_image_dtype(image[, dtype])`​|

## `.Normalize()`​

`.Normalize()`​对图像数据进行标准化处理，核心功能是对输入张量的每个通道执行以下操作：

$$
\text{output} = \frac{(\text{input} - \text{mean})}{\text{std}}
$$

数据会被调整为均值为 0、标准差为 1 的标准正态分布，从而加速模型训练并提升稳定性

- 参数：

  - `mean`​（列表/元组）：各通道的均值

    - ImageNet 的 RGB 均值通常为 `[0.485, 0.456, 0.406]`​（全局统计量）
  - `std`​（列表/元组）：各通道的标准差

    - ImageNet 的 RGB 标准差为 `[0.229, 0.224, 0.225]`​（全局统计量）
  - `inplace`​（布尔值，默认为 `False`​）：若为 `True`​，则直接修改输入张量，否则返回新张量
- 要求：

  - **输入要求**：`Normalize`​ 的输入必须是 `torch.Tensor`​，且需在 `ToTensor()`​ 之后调用（因 `ToTensor()`​ 会将像素值缩放到 [0,1]）
  - **通道匹配**：`mean`​ 和 `std`​ 的长度必须与输入张量的通道数一致
- 功能与优势：

  - **标准化数据分布**：消除通道间的量纲差异，使模型更容易收敛
  - **加速训练**：优化器在标准化后的数据上更易找到梯度方向，减少梯度爆炸/消失问题
  - **泛化能力**：通过固定全局统计量（如 ImageNet 的均值和标准差），确保数据预处理的一致性

## `.RandomResizedCrop()`​

`.RandomResizedCrop()`​用于随机裁剪并调整图像大小

- 参数：

  - `size(int/list/tuple)`​：输出图像的尺寸，若为整数则生成正方形图像 `(size, size)`​，若为元组则按 `(height, width)`​ 输出
  - `scale(tuple, optional)`​：控制裁剪区域占原图的比例范围，默认 `(0.08, 1.0)`​，例如 `(0.2, 0.8)`​ 表示裁剪面积占原图的 20%\~80%。
  - `ratio(tuple, optional)`​：裁剪区域的宽高比范围，默认 `(3./4, 4./3)`​（即 0.75\~1.33），若设为 `(1, 1)`​ 则强制正方形裁剪
  - `interpolation(str/enum)`​：图像缩放的插值方法，常见选项包括：

    - `"bilinear"`​（双线性插值，默认）
    - `"nearest"`​（最近邻插值）
    - `"bicubic"`​（双三次插值）

  - `max_attempts(int, optional)`​：尝试生成有效裁剪区域的最大次数，若失败则回退到中心裁剪
- **工作原理：**

  1. **随机生成裁剪区域**：

      - 根据 `scale`​ 随机选择裁剪面积比例
      - 根据 `ratio`​ 计算宽高比（如 `w/h = 1.2`​），并调整宽高值
      - 随机选择裁剪区域的左上角坐标 `(i, j)`​
  2. **调整大小**：将裁剪后的区域缩放到 `size`​ 指定的尺寸，使用 `interpolation`​ 方法插值
- **示例代码**

  ```python
  from torchvision.transforms import RandomResizedCrop
  
  transform = RandomResizedCrop(
      size=224,
      scale=(0.08, 1.0),
      ratio=(0.75, 1.33),
      interpolation="bilinear"
  )
  ```
- **注意事项：**

  - **多图像一致性**：若处理批量图像（如多模态数据），需确保所有图像的裁剪参数一致
  - **极端比例限制**：当 `ratio`​ 或 `scale`​ 设置过于极端时，可能无法生成有效裁剪区域（此时依赖 `max_attempts`​ 或回退机制）

## `.ColorJitter()`​

`.ColorJitter()`​常用图像数据增强方法，用于随机调整图像的亮度、对比度、饱和度和色调

- **参数：**

  - `brightness(float/tuple)`​：亮度调整范围，**要求**值必须为非负数

    - 若为浮点数则从 `[max(0, 1 - brightness), 1 + brightness]`​ 随机采样
    - 若为元组（如 `(0.8, 1.2)`​），则直接指定范围 `[min, max]`​
  - `contrast(float/tuple)`​：对比度调整范围，规则同 `brightness`​，范围计算方式相同
  - `saturation(float/tuple)`​：饱和度调整范围，规则同 `brightness`​
  - `hue(float/tuple)`​：色调调整范围

    - 若为浮点数则从 `[-hue, hue]`​ 随机采样
    - 若为元组，需满足 `-0.5 ≤ min ≤ max ≤ 0.5`​
- **注意事项：**

  1. **参数顺序问题**：若使用元组（如 `(min, max)`​），必须保证 `min ≤ max`​，否则会报错
  2. **输入图像限制**：对于色调调整（`hue`​），输入图像的像素值需为非负数（如未归一化的 RGB 图像）
  3. **框架差异**
- **PyTorch 示例：**

  ```python
  from torchvision import transforms
  
  transform = transforms.ColorJitter(
      brightness=(0.8, 1.2),  # 亮度范围 80%~120%
      contrast=0.3,           # 对比度范围 70%~130%
      saturation=(0.5, 1.5),  # 饱和度范围 50%~150%
      hue=0.1                 # 色调范围 -0.1~0.1
  )
  ```

## `torch.compile()`加速转换

可优化计算图和内存布局来提升性能，同时减少内存格式变量的影响

原理：将相邻操作合并为单一内核调用，减少中间内存分配和访存开销，将固定值内联到计算图中避免重复计算等

```python
# 单独编译Normalize操作
normalize = transforms.Normalize(mean=[0.5], std=[0.5])
compiled_normalize = torch.compile(normalize)  # 优化计算和内存访问

# 使用编译后的Normalize
input_tensor = torch.rand(1, 3, 224, 224)  # 假设输入为NCHW格式
output = compiled_normalize(input_tensor)   # 自动适配最优内存布局
```



# `torchvision.utils`​

主要用于图像处理和可视化

## `.make_grid()`​图像网格生成

将一批图像张量排列成网格形式，便于可视化

参数：

- `tensor`​：输入图像张量（形状为 B×C×H×W）
- `nrow=8`​：每行显示的图像数量
- `padding=2`​：图像间的像素间距
- `normalize=False`​：若为True则将图片的像素值归一化处理
- `range=None`​：若为`(min, max)`​则用min和max来规范化image
- `scale_each=False`​：若为True则每个图片独立规范化，而不是根据所有图片的像素最大最小值来规范化

```python
from torchvision.utils import make_grid
grid = make_grid(images, nrow=4, padding=10)
plt.imshow(grid.permute(1, 2, 0))  # 显示网格
```

## `.save_image()`​**图像保存**

将张量保存为图像文件，支持批量保存

**参数**：

- `tensor`​：输入张量（单张或批量图像）
- `filename`​：保存路径
- `format`​：文件格式（如 `'png'`​、`'jpeg'`​）

```python
save_image(images, 'batch_grid.png', nrow=4)
```

## `.draw_bounding_boxes()`​边界框绘制

在图像上绘制目标检测的边界框，支持多框和多类别标签

**参数**：

- `image`​：输入图像张量（$C \times H \times W$）
- `boxes`​：边界框坐标张量（形状为 $N \times 4$，格式为 `[xmin, ymin, xmax, ymax]`​）
- `labels`​：可选，每个框的标签列表
- `colors`​：框的颜色（支持自定义）

```python
from torchvision.utils import draw_bounding_boxes
img_with_boxes = draw_bounding_boxes(image, boxes, labels=['cat', 'dog'], colors='red')
```

##  **​`.draw_segmentation_masks`​**​**分割掩码可视化**

在图像上叠加语义分割的掩码（mask），支持多类别

**参数**：

- `image`​：输入图像张量
- `masks`​：二值掩码张量（形状为 $N \times H \times W$，$N$ 为类别数）
- `alpha`​：掩码透明度（默认为0.5）

```python
masks = torch.rand(3, 256, 256) > 0.5  # 模拟3类掩码
result = draw_segmentation_masks(image, masks, alpha=0.6)
```

‍
