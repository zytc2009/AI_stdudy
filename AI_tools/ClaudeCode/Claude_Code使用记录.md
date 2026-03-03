[toc]

## 安装配置

### 安装

个人喜欢CLI，我的电脑是win11

```
Windows 10（2004+）或 Windows 11
管理员权限
已安装 Git
Node.js 18+（推荐 LTS）
稳定网络环境
```

打开PowerShell（如果失败，请以管理员身份运行）

```
#安装
npm install -g @anthropic-ai/claude-code
#验证一下
claude --version  
#启动claude，开始首次的环境配置，最好执行前切换到我的工作目录
claude
```

让你选择主题配色，选择一个，回车

选择登录方式：3种计费方式。 选择 **Anthropic 控制平台（推荐）**，会自动跳转到浏览器。

- 需要一个 Google 账号（Gmail）
- 登录后授权
- 复制一段 Code 回到终端

如果没有 Google 账号，可以自行注册，或购买一个（价格通常 5–10 元）。

回到CMD会提醒你选择官方还是环境变量 API KEY,选择后者。

然后会让你确认当前目录是否安全可用，如果是你的工作目录，就回车，否则退出，切换你的目录，然后重新执行claude

我因为是试用期，所以没有配置key，后续购买key之后再完善这一部分，TODO

网上的说是需要更新%userprofile% \ .claude \settings.json, 待验证

```
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "你的令牌",
    "ANTHROPIC_BASE_URL": "https://api.zhangsan.cool",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "claude-sonnet-4-5-20250929",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "claude-sonnet-4-5-20250929",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "claude-sonnet-4-5-20250929",
    "ANTHROPIC_MODEL": "claude-sonnet-4-5-20250929"
  },
  "includeCoAuthoredBy": false
}

```

然后出现欢迎页面，开始项目了，

输入/help, 查看可用功能.  输入 **/** 会列出可用的命令.

**重要特性:**

- **Claude Code 会根据需要自动读取文件** - 你不需要手动添加上下文
- **Claude 可以访问自己的文档** - 可以回答关于功能和特性的问题

### 交互模式

- **Ask（询问）**：只看不动
- **Plan（规划）**：只规划，不执行
- **Edit（编辑）**：直接执行

终端执行其实不用手动切换，Claude 会自动切换，在 VS Code 插件中可以手动切换模式





### 配置模板参考：everything-claude-code

```
# 添加市场
/plugin marketplace add affaan-m/everything-claude-code
# 安装插件
/plugin install everything-claude-code@everything-claude-code
```

或者直接添加到你的 `~/.claude/settings.json`：

```
{
  "extraKnownMarketplaces": {
    "everything-claude-code": {
      "source": {
        "source": "github",
        "repo": "affaan-m/everything-claude-code"
      }
    }
  },
  "enabledPlugins": {
    "everything-claude-code@everything-claude-code": true
  }
}
```

Claude Code 插件系统不支持通过插件分发 `rules`。你需要手动安装规则：

```
#安装规则: 选项A（应用于所有项目）
cp -r everything-claude-code/rules/* ~/.claude/rules/
# 选项B：项目级规则（仅应用于当前项目）
mkdir -p .claude/rules
cp -r everything-claude-code/rules/* .claude/rules/

#开始使用
# 尝试一个命令（插件安装使用命名空间形式）
/everything-claude-code:plan "添加用户认证"

# 手动安装（选项2）使用简短形式：
# /plan "添加用户认证"

# 查看可用命令
/plugin list everything-claude-code@everything-claude-code
```

 你现在可以使用 13 个代理、43 个技能和 31 个命令。

关注重点配置：

```
agents/           # 用于委托的专业子代理
   |-- planner.md           # 功能实现规划
   |-- architect.md         # 系统设计决策
   |-- tdd-guide.md         # 测试驱动开发
   |-- code-reviewer.md     # 质量和安全审查
   |-- security-reviewer.md # 漏洞分析
   |-- refactor-cleaner.md  # 死代码清理
   |-- doc-updater.md       # 文档同步
   |-- go-reviewer.md       # Go 代码审查（新增）
   |-- go-build-resolver.md # Go 构建错误解决（新增）
skills/           # 工作流定义和领域知识
   |-- coding-standards/           # 语言最佳实践
   |-- backend-patterns/           # API、数据库、缓存模式
   |-- continuous-learning/        # 从会话中自动提取模式（详细指南）
   |-- continuous-learning-v2/     # 基于直觉的学习与置信度评分
   |-- iterative-retrieval/        # 子代理的渐进式上下文细化
   |-- strategic-compact/          # 手动压缩建议（详细指南）
   |-- tdd-workflow/               # TDD 方法论
   |-- verification-loop/          # 持续验证（详细指南）
   |-- cpp-testing/                # C++ 测试模式、GoogleTest、CMake/CTest（新增）
rules/            # 始终遵循的指南
   |-- security.md         # 强制性安全检查
   |-- coding-style.md     # 不可变性、文件组织
   |-- testing.md          # TDD、80% 覆盖率要求
   |-- git-workflow.md     # 提交格式、PR 流程
   |-- agents.md           # 何时委托给子代理
   |-- performance.md      # 模型选择、上下文管理
contexts/         # 动态系统提示注入上下文（详细指南）
  |-- dev.md              # 开发模式上下文
  |-- review.md           # 代码审查模式上下文
  |-- research.md         # 研究/探索模式上下文
```



## 工作流

工作流分四阶段： Research → Plan → Annotate (循环1-6次) → Implement → Feedback

1、Research：深度阅读 每次任务前，要求 AI 深读相关代码，将发现写入 research.md。Prompt 中 "deeply"、"in great details" 是必要信号，缺了 AI 就浅尝辄止。写成文件是为了建立审查面——AI 编码中最昂贵的失败不是 bug，而是脱离上下文的正确实现。 

2、Plan：结构化规划 审阅 research 后，让 AI 生成 plan.md，含方法论、代码片段、文件路径、权衡考量。不用内置 Plan Mode，Markdown 文件可自由编辑且持久存在。有开源参考实现时一并提供，AI 输出质量会显著提升。 

3、Annotate：注释循环（核心） 在 plan 文档中直接添加行内注释，让 AI 据此更新，反复 1-6 轮。注释可以是两个字 "not optional"，也可以是整段业务约束或结构重定向。每次必须附加 "don't implement yet"，否则 AI 会擅自动手。 Markdown 文件充当人与 AI 的共享可变状态，比在聊天中来回指导高效得多。AI 擅长理解代码和写实现，但不知道你的产品优先级和工程权衡底线——注释循环就是注入判断力的通道。

 4、Implement：执行 所有决策完成后，一句 "implement it all" 启动执行。此时创造性工作已结束，实现应该是机械的。角色切换为监督者，反馈极短："wider"、"still cropped"。方向走偏时不修补，revert 后缩小范围重做。 一句话：把 AI 当能力极强但缺乏业务判断的工程师——你决策，它执行。

## 提案生成

语音转录倾倒需求，图形页面问关键问题，生成详细实施计划，绕过权限自动构建，配置SuperBase+stripe



## MCP使用

| 命令                       | 作用                             |
| :------------------------- | :------------------------------- |
| `claude mcp add`           | 添加一个 MCP 服务器              |
| `claude mcp list`          | 查看所有已配置服务器             |
| `claude mcp get <name>`    | 查看某个服务器详情               |
| `claude mcp remove <name>` | 删除服务器                       |
| `/mcp`                     | 在 Claude Code 中查看状态 / 认证 |

### 安装 MCP 服务器

MCP 服务器支持 HTTP/SSE/stdio 三种接入方式，推荐优先使用 HTTP（SSE已弃用）。

#### 1. 远程 HTTP 服务器（推荐）

适用于云服务类工具，是最通用的方式：

```
# 基础语法
claude mcp add --transport http <服务器名称> <服务器URL>

# 示例1：连接Notion
claude mcp add --transport http notion https://mcp.notion.com/mcp

# 示例2：带身份验证的HTTP服务器
claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer 你的令牌"
```

#### 2. 本地 stdio 服务器

适用于需要本地系统访问的工具（如本地数据库、自定义脚本）：

```
# 基础语法（注意：--前是Claude参数，--后是服务器命令）
claude mcp add --transport stdio [--env 环境变量] <服务器名称> -- <启动命令>

# 示例：连接Airtable（需替换自己的API密钥）
claude mcp add --transport stdio --env AIRTABLE_API_KEY=你的密钥 airtable \
  -- npx -y airtable-mcp-server
```

> 关键注意：`--transport`/`--env` 等参数必须放在服务器名称**前面**，`--` 用于分隔Claude参数和服务器命令，避免参数冲突。

### 管理 MCP 服务器

配置完成后，你可以通过以下命令管理服务器：

```
# 列出所有已配置的服务器
claude mcp list

# 查看指定服务器详情（如github）
claude mcp get github

# 删除指定服务器
claude mcp remove github

# 在Claude Code中检查服务器状态
/mcp
```

### 配置范围（控制服务器可见性）

你可以指定 MCP 服务器的生效范围，适配个人/团队使用场景：

| 范围          | 用途                                      | 配置命令示例                         |
| :------------ | :---------------------------------------- | :----------------------------------- |
| local（默认） | 仅当前项目可用，私密配置（如敏感密钥）    | `claude mcp add --scope local ...`   |
| project       | 团队共享（存储在.mcp.json，可提交版本库） | `claude mcp add --scope project ...` |
| user          | 所有项目可用（个人全局配置）              | `claude mcp add --scope user ...`    |

> 优先级：local > project > user（同名服务器，本地配置覆盖共享配置）

### 实用示例

#### 示例 1：GitHub 代码审查

```
claude mcp add --transport http github https://api.githubcopilot.com/mcp/
```

在 Claude Code 中：

```
> Review PR #456 and suggest improvements
> Show me all open PRs assigned to me
```

#### 示例 2：Sentry 生产环境排错

```
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
> /mcp   # 完成 OAuth 登录
> What are the most common errors in the last 24 hours?
```

### 在对话中使用 MCP 的高级方式

#### 1、使用 **@** 引用 MCP 资源

```
> Analyze @github:issue://123 and suggest a fix
```

支持多个资源对比：

```
> Compare @postgres:schema://users with @docs:file://user-model
```

#### 2、MCP Prompt 作为斜杠命令

MCP 可以暴露命令：

```
/mcp__github__list_prs
/mcp__jira__create_issue "Login bug" high
```

Claude 会像执行内置命令一样执行它们。

### 关键注意事项

1. **身份验证**：远程MCP服务器（如GitHub/Sentry）需在Claude Code中执行 `/mcp` 完成OAuth 2.0授权；
2. **Windows兼容**：本地stdio服务器若用npx，需加`cmd /c`包装（如`-- cmd /c npx -y 包名`），否则会报"Connection closed"错误；
3. **第三方风险**：使用非官方MCP服务器时，需确认来源可信，避免提示注入/安全风险；
4. **参数顺序**：stdio服务器配置时，`--` 前后的参数不可颠倒，否则会执行失败。



## 子代理

**子代理**是运行在**独立上下文窗口**中的专用 AI 助手, 用于处理特定类型的任务。

三类：研究(隔离上下文，节省token)，代码审查(客观)，QA测试(自动化)

每个子代理都可以拥有：

- 独立的系统提示（System Prompt）
- 独立的上下文（不污染主对话）
- 指定的模型（Sonnet / Haiku / Opus）
- 明确的工具访问权限
- 独立的权限模式
- 生命周期钩子（Hooks）

当 Claude 判断你的请求**符合某个子代理的描述（description）**时，就会自动将任务委托给该子代理，由它独立完成并返回结果。

子代理的核心价值在于**隔离 + 专业化**。

### 内置的子代理:

| 代理              | 说明                           |
| :---------------- | :----------------------------- |
| Explore           | 探索代理: 只读搜索与分析代码库 |
| Plan              | 规划:计划模式下的代码库研究    |
| General-purpose   | 通用代理: 复杂、多步骤任务     |
| Bash              | 在独立上下文中运行命令         |
| statusline-setup  | 配置状态栏                     |
| Claude Code Guide | 解答 Claude Code 使用问题      |

### 创建子代理

推荐方式：使用 `/agents` 命令， 填写描述，权限，模型，允许的工具，注入的技能(skill)

```
---
name: code-reviewer
description: Reviews code for quality and best practices
tools: Read, Grep, Glob
model: sonnet
permissionMode: default
---

You are a senior code reviewer.
Analyze code and provide actionable feedback.
```

**常用字段说明**

| 字段            | 作用                            |
| :-------------- | :------------------------------ |
| tools           | 允许使用的工具                  |
| disallowedTools | 明确禁止的工具                  |
| model           | haiku / sonnet / opus / inherit |
| permissionMode  | 权限行为控制                    |
| skills          | 注入技能                        |
| hooks           | 生命周期钩子                    |

### 使用模式

#### 1、隔离高输出任务

```
使用子代理运行测试，只返回失败的测试
```

#### 2、并行研究

```
并行使用子代理分析认证、数据库和 API 模块
```

#### 3、串联子代理

```
先用 code-reviewer 找问题，
再用 optimizer 修复问题
```





## Agents Teams模式

 可视化监控：tmux分屏模式

配置**statusline**：可以显示上下文状态

```
/statusline-setup
```

### 与 Subagents 的区别

| 维度         | 单一 Subagent 模式                                         | Agents Teams (Orchestrator) 模式                             |
| :----------- | :--------------------------------------------------------- | :----------------------------------------------------------- |
| **拓扑结构** | **线性/星型**：主 Agent 直接调用 Subagent 处理单一困难块。 | **层级/网状**：存在明确的管理层，多个 Subagents 并行协作。   |
| **决策逻辑** | 主 Agent 决策“做什么”，Subagent 决策“怎么做”。             | Orchestrator 决策“谁做什么”，Workers 决策“怎么做”，存在双层决策。 |
| **执行流**   | 往往是串行：Subagent A 完成后，主 Agent 再决定是否调用 B。 | 强调并行：同时派发任务给 A、B、C，大幅缩短总耗时。           |
| **适用范围** | 解决单一瓶颈（如：某段代码太长理解不了）。                 | 解决系统性任务（如：全栈功能开发、大型重构、多模块分析）。   |
| **上下文**   | 共享主上下文，容易挤占窗口。                               | Workers 拥有独立上下文窗口，互不污染，Orchestrator 仅保留摘要。 |





## 推荐Skill

最好跟AI一起去解决问题之后，把它弄成我自己专属的专用的Skill。

自己写skill：

### 1. **planing-with-files** 

github.com/OthmanAdi/planning-with-files.  解决Claude Code复杂任务遗忘问题：自动创建三个Markdown文件——任务清单跟踪进度、研究笔记存关键发现、操作日志防死循环。安装只需执行两条命令，完美复刻Manus上下文管理方法。

### 2. Superpowers

github.com/obra/superpowers（官方出品，能拆解任务并生成执行计划） 

### 3. Notebook LM Skill

github.com/PleasePrompto/notebooklm-skill（支持批量导入资料，还能解析YouTube视频）

### 4. dev-browser

安装分两步：1.在Claude Code执行两条命令：/plugins marketplace add sawyerhood/dev-browser 和 /plugins install dev-browser@sawyerhood/dev-browser；2.下载extension.zip解压到固定路径，Chrome打开扩展程序页面启用开发者模式，加载已解压的扩展程序文件夹即可。

### 5.document-skills

AI操作文档天花板，支持自动填表、批量处理Excel、生成PPT和智能阅读PDF 

### 6. find-skill

从海量skill库中精准匹配需求，筛选高评分skill并推荐最佳方案 



### 其他skill：

ralph-loop：AI无限打工模式，自动循环执行任务，持续迭代优化直至完成

skill-creator：官方认证的skill鼻祖，能自动分析工作流程，提取可复用模式，生成标准skill文档 

frontend-design：前端美化神器，提供专业级UI设计和响应式布局 

code-simplifier：屎山代码终结者，自动简化复杂逻辑，消除冗余代码 



## 相关工具

###  1. happy-coder

 手机和网页客户端 for Claude Code & Codex， https://github.com/slopus/happy
 需要本地安装工具+手机安装app，然后app扫描二维码连接，**首次使用需要创建账号**

```
npm install -g happy-coder  
```

启动本地：

```
# Instead of: claude
# Use: happy
happy
```

### 2.**OpenWork**

```
npm install -g openwork-orchestrator

openwork start --workspace /path/to/workspace --approval auto
```





## 注意事项

### **MCP约束**

 不要一次启用所有 MCP， 在项目配置中使用 `disabledMcpServers` 来禁用未使用的。
