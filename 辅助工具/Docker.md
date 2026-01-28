# Docker

把环境配置写成代码

Image 是模板 / 蓝图，Container 是用这个模板启动的一次运行实例，类似于类和对象

- 构建环境命令：

    ```shell
    docker build -t my_yolo .		# 把Dockerfile变成image
    # t 表 tag，
    # .为dockerfile所在目录，即当前目录
    ```

- 运行容器命令：

    ```bash
    # 调试命令
    docker run --gpus all -it my_yolo bash		# 从一个image启动一个容器，并进入
    # --gpus all 把宿主机的GPU暴露给容器，否则容器内torch.cuda.is_available()不可用
    # -i表交互式
    # -t表给一个终端
    # --rm跑完自动删容器
    # bash，覆盖Dockerfile中的CMD，表启动后别跑程序，先给我一个shell
    
    # 实验命令
    docker run --gpus all -v /data:/data my_yolo python train.py
    # -v /data:/data，volume，宿主机目录：容器中目录，即把服务器中的/data映射成容器中的/data，数据不进镜像，不占体积，不会丢
    # 直接跑`python train.py`这个命令，跑完容器自动退出
    ```

- 管理调试命令：

    ```shell
    docker ps	# 看正在运行的容器
    docker exec	-it <id> bash	# 进入一个正在跑的容器
    ```

    

## Dockerfile

### GPU训练/推理

```dockerfile
# ===== 1. 选择一个带 CUDA 的 PyTorch 官方镜像（省 90% 坑）=====
# 你只需要改这一行来换 torch/cuda 版本
FROM pytorch/pytorch:2.1.0-cuda11.8-cudnn8-runtime

# ===== 2. 避免交互式安装卡住 + 基础环境变量（常用）=====
ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# ===== 3. 工作目录：容器里你代码放哪 =====
WORKDIR /workspace

# ===== 4. 按需安装系统依赖（opencv/视频/编译常用）=====
# 说明：
# - git: 拉代码/子模块
# - libgl1, libglib2.0-0: opencv 常见缺失库
# - ffmpeg: 视频读写（需要才保留）
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    ffmpeg \
    libgl1 \
    libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*

# ===== 5. 先拷贝 requirements 再 pip（利用缓存：改代码不重装依赖）=====
COPY requirements.txt /workspace/requirements.txt
RUN pip install -r /workspace/requirements.txt

# ===== 6. 再拷贝代码（改代码时只重这一层，build 更快）=====
COPY . /workspace

# ===== 7. 默认启动命令（可选）=====
# 你也可以不用 CMD，每次 docker run 后面自己写 python train.py
CMD ["python", "train.py"]
```

### 无GPU

```dockerfile
FROM python:3.10-slim

ENV PYTHONUNBUFFERED=1 PIP_NO_CACHE_DIR=1
WORKDIR /workspace

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    ffmpeg \
    libgl1 \
    libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
CMD ["python", "train.py"]
```

