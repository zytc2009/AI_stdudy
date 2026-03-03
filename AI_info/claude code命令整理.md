### 1. 基础启动与退出

这些是使用 Claude Code 最基本的命令。

| 命令                  | 说明                                                       |
| :-------------------- | :--------------------------------------------------------- |
| `claude`              | 在当前目录启动交互式 REPL 会话。                           |
| `claude "你的提示词"` | 快速提问模式。执行一次提示词后自动退出（不进入交互界面）。 |
| `claude -c`           | 继续上一次的会话。                                         |
| `claude --help`       | 查看帮助信息和所有可用参数。                               |
| `claude --version`    | 查看当前版本。                                             |
| `/exit` 或 `Ctrl+C`   | 在交互模式中退出程序。                                     |

- 按会话名/ID 恢复：claude -r "auth-refactor" "Finish this PR"（或 --resume/-r）
- 更新：claude update

- 登录相关：claude auth login|logout|status

- 列出子代理：claude agents

- 配置 MCP：claude mcp

- 远程控制会话：claude remote-control

### 2. 交互模式内部命令

启动 `claude` 后，你可以输入自然语言，也可以使用快速前缀

- /：运行内置命令或 Skills（输入 / 可列表，继续输入可过滤）

- !：直接跑 shell 命令并把输出纳入上下文（例：! npm test）

- @：引用文件路径（触发路径补全，把文件纳入上下文）

| 命令           | 说明                                                         |
| :------------- | :----------------------------------------------------------- |
| `/help`        | 显示帮助信息。                                               |
| `/clear`       | 清除当前会话上下文，开始一个新的对话。                       |
| `/compact`     | **常用**。压缩对话历史以节省 Token。当对话过长时使用，AI 会总结之前的对话并保留关键信息。 |
| `/cost`        | 查看当前会话的 Token 消耗和 API 费用估算。                   |
| `/doctor`      | 检查 Claude Code 的运行环境和健康状况（排错用）。            |
| `/config`      | 打开配置文件或修改设置（如 API Key、模型选择等）。           |
| `/init`        | 在当前项目初始化配置（生成 `CLAUDE.md` 文件）。              |
| `/permissions` | 管理工具权限（如允许执行特定 Shell 命令）。                  |
| `/review`      | 让 Claude 审查最近的更改。                                   |
| `/bug`         | 提交 Bug 报告。                                              |
| /context       |                                                              |

- 会话/上下文：/help /clear(=/reset//new) /compact [instructions] /context /cost /export [file] /rewind(=/checkpoint) /fork [name] /rename [name] /resume [session] /exit

- 项目/记忆：/init（生成/更新 CLAUDE.md）/memory（管理 CLAUDE.md 与 auto-memory）/add-dir <dir>

- Review & Diff：/diff（交互 diff）/review（PR review，需 gh）/pr-comments [PR]（需 gh）/security-review

- 配置/外观：/config(=/settings) /permissions /model [model] /output-style [style] /theme /vim /statusline

- 诊断/集成：/doctor /ide /hooks /plugin /mcp /tasks
- /debug [描述]：读会话调试日志排障

- /simplify [focus...]：并行审查最近改动并自动整理

- /batch <任务>：把大改动拆分并行执行（要求是 git 仓库）

### 3. 文件与上下文管理

Claude Code 需要知道“看”哪些文件。以下是向 AI 提供上下文的常用方法。

*   **自动上下文**：Claude 会自动读取当前目录及子目录的文件结构，并根据对话内容自动读取相关文件。
*   **手动指定文件**：
    *   直接在对话中提到文件名（如 *"Read `src/utils.ts` and fix the bug"*），Claude 会自动读取。
    *   启动时指定：`claude path/to/file.py`。
*   **CLAUDE.md**：
    *   这是 Claude Code 的“记忆文件”。你可以在项目根目录创建 `CLAUDE.md`，写入项目架构、编码规范或常用指令。
    *   Claude 每次启动都会读取该文件作为 System Prompt 的一部分。
    *   使用 `/init` 可以自动生成初稿。

### 4. 高级参数

在启动 `claude` 命令时，可以附加以下参数实现特定功能：

| 参数              | 示例                                        | 说明                                                       |
| :---------------- | :------------------------------------------ | :--------------------------------------------------------- |
| `--print`         | `claude -p "explain this" --print`          | 打印详细输出日志。                                         |
| `--output-format` | `claude --output-format json`               | 设置输出格式（如 json），方便脚本解析。                    |
| `--model`         | `claude --model claude-sonnet-4-20250514`   | 指定使用的模型（默认通常是 Sonnet，复杂任务可指定 Opus）。 |
| `--allowedTools`  | `claude --allowedTools "Bash(npm install)"` | 预先批准某些工具的使用，减少运行时的确认弹窗。             |

- 额外工作目录：--add-dir <dir...>（例：claude --add-dir ..\apps ..\lib）
- 权限模式：--permission-mode plan（配合 --allow-dangerously-skip-permissions 只是“允许出现跳过权限选项”）
- 工具控制：--tools "Bash,Edit,Read" / --allowedTools "Read" "Bash(git diff *)" / --disallowedTools ...

- 调试/详细日志：--debug "api,mcp"，--verbose

- 成本/回合限制（打印模式）：--max-budget-usd 5.00，--max-turns 3

- Worktree：--worktree/-w <name>（需要 git 仓库）

- 危险选项（慎用）：--dangerously-skip-permissions

### 5. 常用工作流示例

#### 场景一：修复 Bug
```bash
# 进入交互模式
claude

# 输入指令（Claude 会自动读取相关代码、修复并运行测试）
> I'm getting a "TypeError" in src/auth.py when I try to log in. Please fix it and run the tests.
```

#### 场景二：理解新项目
```bash
# 让 Claude 先阅读项目文档和结构
> Please read the README.md and explain the architecture of this project to me.
```

#### 场景三：通过管道输入内容
```bash
# 将文件内容通过管道传给 Claude 处理
cat package.json | claude "Add a script for 'dev:watch'"
```

#### 场景四：Git 操作
```bash
# Claude Code 可以执行 Git 命令
> Commit the current changes with a descriptive message.
> Create a new branch named 'feature/login' and switch to it.
```

### 6. 小贴士

1.  **权限确认**：当 Claude 想要执行 Shell 命令（如 `npm install`）或修改文件时，它会询问你。你可以选择 "Yes" (本次允许), "Yes and always allow" (总是允许), 或 "No"。
2.  **上下文限制**：如果项目非常大，Claude 可能无法一次性读取所有文件。建议使用 `CLAUDE.md` 标记重点文件，或者在提问时具体指定文件路径。
3.  **Token 节省**：使用 `/compact` 可以有效防止上下文溢出并节省 API 费用。

### 7. 常用快捷键（Windows 常用）

- 取消生成/输入：Ctrl+C

- 退出：Ctrl+D

- 清屏：Ctrl+L
- 切换详细输出：Ctrl+O

- 打开外部编辑器写提示词：Ctrl+G

- 后台化当前任务/命令：Ctrl+B

- 切换任务列表显示：Ctrl+T

- 反向搜索历史：Ctrl+R

- 切模型：Alt+P

- 多行输入：\ + Enter（通用）；或 Shift+Enter（部分终端需 /terminal-setup）

- 粘贴剪贴板图片（Windows）：Alt+V



---

**安装方法（如果尚未安装）：**

```bash
npm install -g @anthropic-ai/claude-code
```
安装后运行 `claude` 并按提示登录 Anthropic 账号即可使用。