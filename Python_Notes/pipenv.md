# `pipenv`

### **1. 环境管理**

| 命令                  | 功能                             | 示例                  |
| --------------------- | -------------------------------- | --------------------- |
| `pipenv install`      | 创建虚拟环境并生成 `Pipfile`     | `pipenv install`      |
| `pipenv --python 3.9` | 指定 Python 版本创建环境         | `pipenv --python 3.9` |
| `pipenv shell`        | 激活虚拟环境                     | `pipenv shell`        |
| `exit`                | 退出虚拟环境                     | `exit`                |
| `pipenv --rm`         | 删除当前虚拟环境                 | `pipenv --rm`         |
| `pipenv --where`      | 显示项目根目录路径               | `pipenv --where`      |
| `pipenv --venv`       | 显示虚拟环境存储路径             | `pipenv --venv`       |
| `pipenv --py`         | 显示虚拟环境的 Python 解释器路径 | `pipenv --py`         |

------

### **2. 依赖管理**

| 命令                             | 功能                                | 示例                          |
| -------------------------------- | ----------------------------------- | ----------------------------- |
| `pipenv install <package>`       | 安装包并更新 `Pipfile`              | `pipenv install requests`     |
| `pipenv install --dev <package>` | 安装开发依赖包                      | `pipenv install --dev pytest` |
| `pipenv uninstall <package>`     | 卸载包并更新 `Pipfile`              | `pipenv uninstall requests`   |
| `pipenv sync`                    | 根据 `Pipfile.lock` 安装依赖        | `pipenv sync`                 |
| `pipenv update`                  | 更新所有依赖并生成新 `Pipfile.lock` | `pipenv update`               |
| `pipenv update <package>`        | 更新指定包                          | `pipenv update requests`      |
| `pipenv lock`                    | 生成/更新 `Pipfile.lock`            | `pipenv lock`                 |
| `pipenv graph`                   | 显示依赖树                          | `pipenv graph`                |

------

### **3. 运行与部署**

| 命令                                 | 功能                           | 示例                                     |
| ------------------------------------ | ------------------------------ | ---------------------------------------- |
| `pipenv run <command>`               | 在虚拟环境中运行命令           | `pipenv run python app.py`               |
| `pipenv requirements`                | 生成 `requirements.txt`        | `pipenv requirements > requirements.txt` |
| `pipenv install -r requirements.txt` | 从 `requirements.txt` 安装依赖 | `pipenv install -r requirements.txt`     |

------

### **4. 安全检查与配置**

| 命令                              | 功能                            | 示例                                                         |
| --------------------------------- | ------------------------------- | ------------------------------------------------------------ |
| `pipenv check`                    | 检查依赖的安全漏洞              | `pipenv check`                                               |
| `pipenv --pypi-mirror <url>`      | 设置 PyPI 镜像源                | `pipenv --pypi-mirror https://pypi.tuna.tsinghua.edu.cn/simple` |
| `export PIPENV_VENV_IN_PROJECT=1` | 在项目目录内创建 `.venv` 文件夹 | （Linux/macOS）                                              |

------

### **5. 其他实用命令**

| 命令                   | 功能                     | 示例                    |
| ---------------------- | ------------------------ | ----------------------- |
| `pipenv --bare`        | 最小化输出（适用于脚本） | `pipenv --bare install` |
| `pipenv open <module>` | 在编辑器中查看模块源码   | `pipenv open requests`  |

------

### **关键说明**

1. **`Pipfile` vs `Pipfile.lock`**
   - `Pipfile`：记录依赖的抽象描述（如 `flask = "*"`）。
   - `Pipfile.lock`：锁定具体版本和哈希值，确保环境一致性。
2. **虚拟环境位置**
   - 默认路径：
     - Windows: `C:\Users\<user>\.virtualenvs`
     - Linux/macOS: `~/.local/share/virtualenvs`
   - 可通过 `PIPENV_VENV_IN_PROJECT=1` 改为项目目录下的 `.venv`。
3. **依赖分组**
   - `[packages]`：生产依赖
   - `[dev-packages]`：开发依赖（如测试工具）。

------

### **常见问题**

- 安装慢：换国内镜像源（如清华源）或跳过锁定阶段：

  ```bash
  pipenv install --skip-lock
  ```

- **权限错误**：确保虚拟环境目录可写（`chmod 700 ~/.virtualenvs`）

如需进一步调试，可使用 `pipenv --help` 查看完整命令列表
