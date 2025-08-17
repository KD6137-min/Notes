- 操作需先选中单元
- Ctrl+ Enter: 运行本单元
- Shift + Enter: 运行本单元, 选中下个单元
- Alt + Enter: 运行本单元, 在其下插入新单元
- command+/: 注释或取消注释
- 命令模式
  - 命令模式(按键 Esc 开启)
  - Ctrl+ Enter : 运行本单元
  - Y: 单元转入代码状态
  - M: 单元转入markdown状态
  - A: 在上方插入新单元
  - B: 在下方插入新单元
  - DD: 删除选中的单元
- 编辑模式
  - 编辑模式(Enter 键启动)
  - Tab: 代码补全或缩进
  - Shift + Tab: 提示
  - `# `: 设置标题
  - `- `: 设置小节
- display(): 输出富媒体对象更适合可视化, Jupyter每个编辑框默认自动display最后一行
  - print()：输出字符串形式，只能显示为文本
- 特殊命令：
  - `%`命令：又称魔法方法，必须写在单元格的首行
    - 运行在Jupyter当前的虚拟环境中，如`%pip list`返回当前kernel环境的库列表
    - 某些操作会持久生效，重启Notebook仍保持，如`%cd`
    - 多条命令：需用`&&`连接，如`!cmd1 && cmd2`
    - 支持丰富的魔法功能
    - 常用操作：
      - %timeit: 返回代码运行的精准时间
      - %time: 返回代码运行的大概时间
      - %run: 引入文件, 比import更强大
  - `!`命令：
    - 直接调用系统的shell环境，如`!pip list`：返回系统主环境的库列表
    - 临时执行，不会保留上下文
    - 多条命令：需逐行输入或使用`%%bash`/`%%cmd`（整个单元格作为脚本执行）
    - 仅用于直接调用系统命令
  - 总结：持久性操作或内核相关功能用%，临时执行系统命令时用!



## Jupyter内核管理

独立进程，执行代码的核心引擎，负责与前端（如浏览器中的Notebook界面）通信，并解释运行用户输入的代码，支持多种编程语言，每个Kernel对应一个独立的运行时环境，可能关联特定的虚拟环境或解释器

- 查：`jupyter kernelspec list [--json]`，列出所有内核及其配置路径，`--json`查看详细信息

- 创：在目标虚拟环境中运行`python -m ipykernel install --user --name myenv --display-name "My Project Env"`

  - `--name`为内核唯一标识（必填）
  - `--display-name`Jupyter界面中显示的名称
  - 终端中激活的虚拟环境需通过`ipykernel`**显式注册**到Jupyter，才能在Notebook中使用该环境的Python解释器和库

- 删：`jupyter kernelspec remove myenv`，需指定内核标识符

  - 或手动删除对应目录

- 改：

  1. 先定位内核配置文件：`cd $(jupyter kernelspec list | grep myenv | awk '{print $2}')`

  2. 编辑kernel.json：重启后生效

     ```json
     {
         "display_name": "New Display Name",	// 修改显示名称
         "argv": [
             "/path/to/python",	// 确保路径正确
             "-m",
             "ipykernel_launcher",
             "-f",
             "{connection_file}"
         ]
     }
     ```

     

## 交互式环境增强

- `obj?`和`obj??`：

    ```python
    import numpy as np
    np.array?    # 显示docstring，同help()
    np.array??   # 显示源码
    ```

    

