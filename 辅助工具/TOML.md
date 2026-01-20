# TOML

核心规则：

1. **键值对用 `=`**
2. **字符串必须加引号**（`"..."` 或 `'...'`）
3. **结构靠 `[table]` / `[[array-of-tables]]`**，不是缩进
4. `#`行注释 / 行尾注释

## 键值对

`=` 两边空格可有可无，但建议留空格更清晰

```toml
name = "Alice"
age = 23
enabled = true
pi = 3.14159
nothing = false
```

## 数据类型

### 字符串

#### 基本字符串

```toml
title = "Deep Learning"
path = "C:\\Users\\me\\data"   # 反斜杠需要转义
quote = "He said \"hi\""
```

#### 单引号字符串

不转义反斜杠，适合路径

```toml
path = 'C:\Users\me\data'      # 不需要写双反斜杠
```

#### 多行字符串（TOML 的“长文本”）

- 多行基本字符串：`""" ... """`（支持转义）
- 多行字面字符串：`''' ... '''`（不支持转义，原样）

```toml
desc1 = """
第一行
第二行
"""

desc2 = '''
路径 C:\Users\me\data 不会处理转义
'''
```

> [!caution]
>
> 这点和 YAML 的 `|` / `>` 不一样，TOML 的多行就是“真的多行字符串”，不会折叠换行

### 整数

```toml
a = 42
b = 1_000_000   # 下划线只是可读性

# 也支持不同进制：
hex = 0xDEADBEEF
oct = 0o755
bin = 0b101010
```

### 浮点

```toml
lr = 0.001
mass = 1.2e3
```

### 布尔

```toml
debug = true
use_amp = false
```

### 日期与时间

TOML 支持明确的日期/时间类型（这也是它比 YAML 更“不歧义”的地方）：

```toml
date = 2026-01-11
time = 19:30:00
dt   = 2026-01-11T19:30:00
dtz  = 2026-01-11T19:30:00-07:00
utc  = 2026-01-12T02:30:00Z
```

想让它变成“字符串”就加引号：

```toml
dt_as_string = "2026-01-11T19:30:00"
```

## 数组

### 基本数组：同类型

```toml
layers = [64, 128, 256]
names  = ["a", "b", "c"]
flags  = [true, false, true]
```

**TOML 数组要求同类型**（常见实现会强约束

### 多行数组

```toml
include = [
  "src/**",
  "tests/**",
  "README.md",
]
```

## 表

用来组织层级结构

### 单层表

```toml
[model]
name = "yolov8"
pretrained = true
```

等价于一个嵌套对象：`model.name`, `model.pretrained`

### 嵌套表

TOML 的“层级”不是靠缩进，而是靠 `[a.b.c]` 这种路径

```toml
[model.backbone]
type = "resnet50"
depth = 50
```

`model` 是一个表，`model.backbone` 是 `model` 里面的子表

## 表数组

列表每项是一个对象”，用 `[[...]]`：

```toml
[[augmentations]]
name = "flip"
p = 0.5

[[augmentations]]
name = "color_jitter"
brightness = 0.2
contrast = 0.2
```

等价于：

```json
{
  "augmentations": [
    {"name":"flip","p":0.5},
    {"name":"color_jitter","brightness":0.2,"contrast":0.2}
  ]
}
```

## 行内表

```toml
optimizer = { type = "adamw", lr = 0.001, weight_decay = 0.01 }
```

短，但不好维护、不好 diff，**能不用就不用**

## Key 的写法

### 裸键

```toml
batch_size = 16
```

### 有特殊字符/空格/点号时：要用引号键

```toml
"batch size" = 16
"my.key" = "literal key name, not nesting"
```

## 常见坑

> [!caution]
>
> - 重复定义同一个键：通常会报错（或后者覆盖，依实现）
> - 先写子表再写父表或顺序冲突：某些组合会导致“表已定义”的错误
> - 数组混类型：尽量保持同类型
> - 以为缩进代表结构：TOML 里缩进只是好看，不影响层级

## 最佳实践

```toml
# =========================
# Project Config
# =========================

[project]
name = "uav-det"
version = "0.1.0"
run_id = "2026-01-11-exp01"   # 想固定为字符串就加引号
seed = 3407

[dataset]
train = "data/train"
val = "data/val"
num_classes = 10
classes = ["car", "person", "truck"]

[model]
name = "yolov8"
pretrained = true

[model.backbone]
type = "resnet50"

[training]
epochs = 100
batch_size = 16
amp = true

[training.optimizer]
type = "adamw"
lr = 0.001
weight_decay = 0.01

[training.scheduler]
type = "cosine"
warmup_epochs = 5

[logging]
log_dir = "runs/"
save_every = 5
start_time = 2026-01-11T19:30:00-07:00  # datetime 类型（带时区）

# 一个“列表里是对象”的典型例子
[[augmentations]]
name = "flip"
p = 0.5

[[augmentations]]
name = "mosaic"
p = 0.2
```

## 常见的 TOML

`pyproject.toml`：

```toml
[project]
name = "my_pkg"
version = "0.1.0"
dependencies = ["numpy", "torch"]

[tool.ruff]
line-length = 100

[tool.black]
line-length = 100
```