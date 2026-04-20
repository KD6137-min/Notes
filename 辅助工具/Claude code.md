```text
/init
/clear
esc中断任务

@路径/文件 : 上下文很重要，可用tab补全
shift+tab,重复两次进入计划模式，先写计划再执行

用ai生成ai提示词
```





## ⌨️ 键盘快捷键

| 快捷键                   | 作用                                                 |
| ------------------------ | ---------------------------------------------------- |
| **Shift + Tab**          | 循环切换权限模式：普通模式 → 自动接受编辑 → 计划模式 |
| **Esc**                  | 中断 Claude 当前响应，回到输入状态                   |
| **Esc, Esc**（连按两次） | 返回并编辑你的上一条消息                             |
| **Ctrl + C**             | 取消当前输入；在空提示符下退出                       |
| **Ctrl + R**             | 展开完整对话记录视图                                 |
| **↑ / ↓**                | 浏览历史输入记录                                     |
| **?**                    | 显示当前终端/IDE 环境中可用的所有快捷键              |

------

## 🔣 特殊符号前缀

| 符号             | 作用                                                   |
| ---------------- | ------------------------------------------------------ |
| `@` + 路径       | 在提示中引用文件或目录（如 `@src/utils.ts`）           |
| `#` + 文字       | 将该指令保存到 `CLAUDE.md` 记忆文件                    |
| `/`              | 打开斜杠命令菜单，继续输入可过滤                       |
| `!` + Shell 命令 | 直接在对话上下文中执行 Shell 命令（如 `! git status`） |

------

## 💬 /斜杠命令完整列表

| 命令           | 作用                                                         |
| -------------- | ------------------------------------------------------------ |
| `/help`        | 列出所有可用命令                                             |
| `/init`        | 扫描代码库并生成初始 `CLAUDE.md`                             |
| `/clear`       | 清空对话历史，开始全新对话（项目记忆保留）。别名：`/reset`、`/new` |
| `/compact`     | 压缩对话历史以释放上下文空间，可带参数指定保留内容           |
| `/btw`         | 提一个不进入主对话、不消耗上下文的临时问题                   |
| `/rewind`      | 将对话和代码回滚到更早的检查点                               |
| `/model`       | 查看或切换当前使用的模型                                     |
| `/cost`        | 查看本次会话的 token 用量和费用                              |
| `/context`     | 可视化当前上下文窗口的内容分布                               |
| `/memory`      | 查看或编辑当前生效的 `CLAUDE.md` 文件                        |
| `/add-dir`     | 为本次会话授予对额外目录的文件访问权限                       |
| `/permissions` | 查看或修改哪些操作需要人工审批                               |
| `/config`      | 打开配置（主题、默认值、编辑器模式）。别名：`/settings`      |
| `/plan`        | 直接进入计划模式，可附带任务描述                             |
| `/diff`        | 打开交互式查看器，查看未提交的变更和每轮的 diff              |
| `/copy`        | 将最后一条回复或代码块复制到剪贴板                           |
| `/export`      | 将当前对话保存为文件或复制到剪贴板                           |
| `/mcp`         | 管理 MCP 服务器连接和认证                                    |
| `/agents`      | 列出、创建或编辑子代理                                       |
| `/hooks`       | 查看工具事件的 hook 配置                                     |
| `/skills`      | 列出当前会话可用的技能                                       |
| `/simplify`    | 并行代理审查最近改动的代码，检查质量并应用修复               |
| `/doctor`      | 诊断安装和环境问题                                           |
| `/resume`      | 重新打开上一次会话并从上次离开的地方继续。别名：`/continue` 或在开始claude时使用`claude --resume` |
| `/exit`        | 退出 CLI。别名：`/quit`                                      |

------

## 🗺️ 计划模式（Plan Mode）

**计划模式**是一种只读模式，Claude 在此模式下只会探索、解释和提出方案，不会修改文件或执行命令。适合在让 Claude 真正动手前先审查它的思路。

典型工作流：进入计划模式 → 反复打磨计划 → 切换到自动接受编辑模式 → Claude 执行。进阶技巧：让一个 Claude 写计划，再开第二个 Claude 以资深工程师角色来审查它。一旦出现偏差，立即切回计划模式重新规划，而不是在执行中途强行纠偏。

------

## 🤖 多 Agent（子代理）使用

在任意请求后追加"**use subagents**"，让 Claude 为这个问题投入更多算力。将独立任务交给子代理，以保持主代理的上下文窗口干净专注。

在 `.claude/agents/` 目录下创建 `.md` 文件来定义子代理：

```markdown
# .claude/agents/code-reviewer.md
---
name: code-reviewer
description: Expert code reviewer. Use proactively after code changes.
tools: Read, Grep, Glob, Bash
model: sonnet
---
你是一名资深代码审查员……
```

还可以给子代理加上 `isolation: worktree` 前缀，让它在独立的 git worktree 中运行，特别适合大规模批量修改。然后直接说：*"将所有同步 IO 迁移为异步，批量启动 10 个并行 worktree 隔离的子代理，每个测完自己的改动后发一个 PR。"*

------

## ⚡ 并行工作流

最大的生产力突破是同时运行 3–5 个 Claude 会话，每个在独立的 git worktree 中工作。运行 `claude --worktree` 在隔离的 worktree 中启动会话，加上 `--tmux` 可在独立的 Tmux 窗口中启动。用 `/batch` 命令可以将大型迁移任务自动分拆给数十甚至数百个并行代理，每个代理独立测试并发 PR。

新建.trees目录，

git worktree add .trees/branch1

git worktree add .trees/branch2

为每个分支新开一个终端

让claude add并commit、merge，自己处理冲突，合并完成后自己删除.trees文件夹并push

------

## 🧠 CLAUDE.md 记忆系统

将一个 `CLAUDE.md` 文件放在仓库根目录并提交到 git，全团队共同维护。**核心实践：每次 Claude 做错了什么，就把这条规则加进 `CLAUDE.md`**，让它下次不再重犯。每次纠错后追加一句："更新你的 CLAUDE.md，确保不再犯这个错误。"

还可以安装 GitHub Action（`/install-github-action`），在 PR 评论中 `@claude` 来自动更新 CLAUDE.md，实现"复利工程"——每次纠错都让之后每个会话更好。

------

## 🪝 Hooks 自动化

| 事件                | 典型用途                                       |
| ------------------- | ---------------------------------------------- |
| `SessionStart`      | 每次启动时动态加载上下文                       |
| `PreToolUse`        | 记录所有 bash 命令日志                         |
| `PostToolUse`       | 写完/编辑后自动格式化，防止 CI 失败            |
| `PermissionRequest` | 将权限请求路由到 Slack / Opus 审核             |
| `Stop`              | 长任务结束后运行确定性检查，或提示 Claude 继续 |
| `PostCompact`       | 上下文压缩后重新注入关键指令                   |

------

## 🔑 核心验证技巧

**给 Claude 一种自我验证的方式**，是提升输出质量最有效的单一做法。如果 Claude 能自己关闭反馈循环，它会一直迭代直到结果正确。前端工作推荐安装 Claude Code Chrome 扩展，让它能在浏览器中亲眼检查页面效果。

几个好用的强迫验证的 Prompt：

- *"Grill me on these changes and don't make a PR until I pass your test."*（先测试我对变更的理解再发 PR）
- *"Prove to me this works."*（对比 main 和你的分支的行为差异来证明可行）
- *"Knowing everything you know now, scrap this and implement the elegant solution."*（第一次结果不够好时用）