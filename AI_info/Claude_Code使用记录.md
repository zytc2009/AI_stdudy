[toc]

## 安装配置

everything-claude-code

安装：

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



## 子代理

三类：研究(隔离上下文，节省token)，代码审查(客观)，QA测试(自动化)



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



## 快捷键

1. !命令：直接运行!git status等，结果进上下文免切终端 
2. 双击Esc：代码改错一键回退至上一个检查点 
3. Ctrl+R：秒搜历史对话，比翻记事本快 
4. @召唤：像微信@人一样引用文件免复制 
5.  /init：自动生成CLAUDE.md项目说明书 
6. Memory记忆：记住开发偏好如"用bun不用npm" 
7.  --teleport：跨设备无缝接力会话 
8.  /export：导出对话为Markdown文档 
9. Ctrl+S：暂存半截思路自动恢复 
10. Tab补全：接收灰色建议文字省打字 
11. --continue：恢复意外中断的工作流 
12. /context：查看token用量明细 
13. /stats：分析使用习惯和模型偏好











## 注意事项

### **MCP约束**

 不要一次启用所有 MCP， 在项目配置中使用 `disabledMcpServers` 来禁用未使用的。
