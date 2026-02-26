# Claude Code 环境搭建和使用技巧指南

## 目录

- [1. Claude Code 简介](#1-claude-code-简介)
- [2. 环境搭建](#2-环境搭建)
- [3. MCP 使用指南](#3-mcp-使用指南)
- [4. Skill 使用指南](#4-skill-使用指南)
- [5. 自定义 Skill 规范](#5-自定义-skill-规范)
- [6. 自定义 Skill 示例](#6-自定义-skill-示例)
- [7. 使用技巧与最佳实践](#7-使用技巧与最佳实践)

---

## 1. Claude Code 简介

Claude Code 是 Anthropic 公司推出的官方命令行工具，它将 Claude AI 的强大能力直接集成到开发者的终端环境中。与传统的 Claude 网页版不同，Claude Code 能够深度理解您的代码库，执行文件操作，运行命令，并与您的开发工作流无缝集成。这使得开发者可以在熟悉的环境中利用 AI 辅助编程，而无需在浏览器和 IDE 之间频繁切换。

Claude Code 的核心优势在于其对项目上下文的深度理解能力。它能够读取和分析整个代码库的结构，理解文件之间的依赖关系，并根据项目的编码风格和约定提供建议。此外，Claude Code 还支持通过 MCP（Model Context Protocol）扩展其能力，通过 Skill 系统实现特定领域的专业化功能，从而满足不同场景下的开发需求。

### 1.1 主要特性

Claude Code 提供了一系列强大的功能特性，使其成为开发者提高效率的利器。首先，它具备智能代码理解和编辑能力，能够阅读和分析大型代码库，理解代码结构和逻辑，并根据上下文进行精确的代码修改。这种能力不仅限于简单的代码补全，还包括重构、调试和优化建议。

其次，Claude Code 支持命令执行和环境交互。它可以在用户的指导下运行终端命令，如安装依赖、运行测试、执行构建脚本等。这种能力使得 Claude Code 能够参与到完整的开发周期中，从代码编写到测试验证，再到部署准备。

第三，Claude Code 拥有灵活的扩展系统。通过 MCP 协议，Claude Code 可以连接外部数据源和服务，如数据库、API、文件系统等。通过 Skill 系统，用户可以定义特定的任务流程和专业领域知识，使 Claude Code 在特定场景下表现更加专业和高效。

### 1.2 适用场景

Claude Code 在多种开发场景中都能发挥重要作用。在代码开发与重构场景中，它可以帮助开发者快速理解现有代码库，识别代码异味，提供重构建议，并自动执行重构操作。对于复杂的遗留系统，Claude Code 能够帮助新成员快速上手，降低学习曲线。

在调试与问题排查场景中，Claude Code 可以分析错误日志，定位问题根源，并提供修复建议。它能够结合项目上下文，理解错误的上下文环境，从而提供更加精准的诊断和解决方案。

在文档编写与知识管理场景中，Claude Code 可以根据代码自动生成文档，维护 README 文件，创建 API 文档等。这对于需要保持文档与代码同步的项目尤为重要。

在自动化任务处理场景中，Claude Code 可以执行批量操作，如文件重命名、代码格式化、依赖更新等重复性工作，从而释放开发者的时间，专注于更有创造性的工作。

---

## 2. 环境搭建

### 2.1 系统要求

在开始安装 Claude Code 之前，请确保您的系统满足以下基本要求。首先，操作系统方面，Claude Code 支持 macOS 10.15 或更高版本、Windows 10 或更高版本（建议使用 Windows Terminal）、以及主流 Linux 发行版（如 Ubuntu 18.04+、Fedora、Debian 等）。

其次，软件依赖方面，Node.js 是必需的，建议安装 Node.js 18.0 或更高版本。npm 或 yarn 包管理器也需要预先安装。此外，Git 版本控制系统对于大多数开发场景是必需的，建议安装 Git 2.0 或更高版本。

硬件要求方面，虽然 Claude Code 本身对硬件要求不高，但为了保证流畅的使用体验，建议至少有 4GB 可用内存和 1GB 可用磁盘空间。如果需要处理大型代码库，建议配置更高的硬件规格。

### 2.2 安装步骤

Claude Code 的安装过程相对简单，主要有以下几种安装方式。最推荐的方式是通过 npm 全局安装，这是官方推荐的标准安装方法。

```bash
# 通过 npm 安装 Claude Code
npm install -g @anthropic-ai/claude-code

# 或使用 yarn 安装
yarn global add @anthropic-ai/claude-code
```

安装完成后，您可以通过以下命令验证安装是否成功：

```bash
# 检查版本
claude --version

# 查看帮助信息
claude --help
```

对于 macOS 用户，还可以通过 Homebrew 进行安装，这种方式便于后续的版本更新：

```bash
# 添加 Anthropic 的 tap
brew tap anthropics/anthropic

# 安装 Claude Code
brew install claude-code
```

首次运行时，Claude Code 会引导您完成身份认证。您需要拥有 Anthropic 账户并订阅 Claude Pro 或 Claude Team 服务，或者使用 API Key 进行认证。认证过程会打开浏览器窗口，按照提示完成授权即可。

```bash
# 启动 Claude Code
claude

# 使用 API Key 认证
claude --api-key YOUR_API_KEY
```

### 2.3 配置文件详解

Claude Code 使用多种配置文件来管理其行为和功能。理解这些配置文件的作用和结构，对于充分利用 Claude Code 的能力至关重要。

#### 2.3.1 CLAUDE.md 项目配置

`CLAUDE.md` 是 Claude Code 的核心项目配置文件，通常放置在项目根目录。这个文件用于向 Claude 提供项目的背景信息、编码规范、注意事项等上下文内容。Claude Code 在启动时会自动读取并理解这个文件的内容。

```markdown
# CLAUDE.md 示例

## 项目概述
这是一个基于 Next.js 15 的电商网站项目，使用 App Router 架构。

## 技术栈
- Next.js 15 (App Router)
- React 19
- TypeScript 5
- Tailwind CSS
- Prisma (ORM)
- PostgreSQL

## 编码规范
- 使用函数组件和 Hooks
- 所有组件使用 TypeScript
- 遵循 ESLint 和 Prettier 配置
- 组件文件使用 PascalCase 命名
- 工具函数使用 camelCase 命名

## 重要约定
- 所有 API 路由放在 src/app/api/ 目录
- 公共组件放在 src/components/ 目录
- 工具函数放在 src/lib/ 目录
- 数据库模型放在 src/prisma/schema.prisma

## 注意事项
- 修改数据库模型后需要运行 prisma generate
- 不要直接修改 .env 文件，使用 .env.local
```

#### 2.3.2 .claude 目录结构

Claude Code 会在项目中创建 `.claude` 目录，用于存储配置、命令和 Skill 定义。这个目录的结构如下：

```
.claude/
├── commands/          # 自定义命令定义
│   ├── test.md        # /test 命令
│   └── review.md      # /review 命令
├── skills/            # 自定义 Skill 定义
│   ├── my-skill.md    # Skill 配置文件
│   └── skill-name/    # Skill 目录形式
│       ├── skill.md
│       └── templates/
├── settings.json      # Claude Code 设置
└── mcp.json           # MCP 配置（部分版本）
```

#### 2.3.3 全局配置文件

Claude Code 的全局配置文件位于用户主目录下，用于管理跨项目的通用设置：

- **macOS/Linux**: `~/.config/claude-code/settings.json`
- **Windows**: `%APPDATA%\claude-code\settings.json`

```json
{
  "apiProvider": "anthropic",
  "defaultModel": "claude-sonnet-4-20250514",
  "permissions": {
    "allow": [
      "Read(**)",
      "Edit(**)",
      "Write(**)",
      "Bash(npm install)",
      "Bash(npm run *)"
    ],
    "deny": [
      "Bash(rm -rf /*)",
      "Bash(sudo *)"
    ]
  },
  "env": {
    "ANTHROPIC_API_KEY": "your-api-key"
  }
}
```

### 2.4 权限管理

Claude Code 实现了细粒度的权限管理系统，确保 AI 只能在用户授权的范围内执行操作。权限配置分为允许列表和拒绝列表，支持通配符模式匹配。

权限类型包括以下几种：

- **Read**: 读取文件权限，如 `Read(src/**)` 允许读取 src 目录下所有文件
- **Edit**: 编辑文件权限，如 `Edit(**/*.ts)` 允许编辑所有 TypeScript 文件
- **Write**: 创建新文件权限，如 `Write(src/components/**)` 允许在 components 目录创建新文件
- **Bash**: 执行命令权限，如 `Bash(git *)` 允许执行所有 git 相关命令
- **WebFetch**: 网络请求权限，用于控制 Claude 访问外部网络的能力

```json
{
  "permissions": {
    "allow": [
      "Read(**)",
      "Edit(**)",
      "Write(**)",
      "Bash(npm *)",
      "Bash(git *)",
      "Bash(bun *)"
    ],
    "deny": [
      "Bash(rm -rf /)",
      "Bash(sudo rm *)",
      "Read(.env)",
      "Read(**/secrets/**)"
    ]
  }
}
```

---

## 3. MCP 使用指南

### 3.1 MCP 概念介绍

MCP（Model Context Protocol）是由 Anthropic 开发的开放协议，用于标准化 AI 模型与外部数据源和工具之间的交互。通过 MCP，Claude Code 可以连接到各种外部服务，如数据库、文件系统、API、云服务等，从而扩展其能力边界。

MCP 采用客户端-服务器架构。Claude Code 作为 MCP 客户端，可以连接到多个 MCP 服务器。每个 MCP 服务器提供特定的功能，如访问 GitHub、操作数据库、读取文件等。这种架构使得 Claude Code 的能力可以无限扩展，只要存在相应的 MCP 服务器。

MCP 的核心价值在于它提供了一个统一的接口标准，使得不同的工具和服务可以通过相同的方式被 AI 模型访问和操作。这大大降低了集成的复杂性，同时也为社区贡献和生态建设提供了基础。

### 3.2 MCP 配置方法

MCP 的配置主要通过配置文件完成。在 Claude Code 中，MCP 配置通常存储在项目级别的 `.mcp.json` 文件或全局级别的 `~/.config/claude-code/mcp.json` 文件中。

#### 3.2.1 配置文件格式

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/path/to/allowed/directory"
      ],
      "env": {
        "NODE_OPTIONS": "--max-old-space-size=4096"
      }
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "your-github-token"
      }
    },
    "postgres": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://user:password@localhost:5432/dbname"
      ]
    },
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"]
    }
  }
}
```

#### 3.2.2 配置项说明

每个 MCP 服务器的配置包含以下字段：

- **command**: 启动 MCP 服务器的命令，通常是 `npx`、`node` 或可执行文件路径
- **args**: 传递给命令的参数数组
- **env**: 环境变量配置，用于传递敏感信息如 API Token
- **disabled**: 是否禁用该服务器，默认为 `false`

### 3.3 常用 MCP 服务器

MCP 生态系统中有许多官方和社区维护的服务器，以下是几个最常用的 MCP 服务器及其用途介绍。

#### 3.3.1 文件系统服务器 (filesystem)

文件系统服务器允许 Claude Code 访问本地文件系统，这是最基础也是最常用的 MCP 服务器之一。通过配置允许访问的目录，Claude 可以读取、创建和修改文件。

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/home/user/projects",
        "/home/user/documents"
      ]
    }
  }
}
```

#### 3.3.2 GitHub 服务器

GitHub 服务器使 Claude Code 能够与 GitHub API 交互，执行如创建仓库、管理 Issues、创建 Pull Requests、查看代码等操作。这对于需要与 GitHub 工作流集成的项目非常有用。

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_your_token_here"
      }
    }
  }
}
```

#### 3.3.3 数据库服务器

MCP 提供了多种数据库服务器的支持，包括 PostgreSQL、SQLite、MySQL 等。这使得 Claude Code 能够直接查询和操作数据库，对于数据分析和后端开发场景非常有价值。

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://localhost:5432/mydb"
      ]
    },
    "sqlite": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-sqlite",
        "--db-path",
        "/path/to/database.db"
      ]
    }
  }
}
```

#### 3.3.4 Puppeteer 服务器

Puppeteer 服务器为 Claude Code 提供了浏览器自动化能力，可以执行网页截图、生成 PDF、执行页面操作、爬取网页内容等任务。

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"]
    }
  }
}
```

### 3.4 MCP 调试与故障排查

当 MCP 服务器出现问题时，可以通过以下方式进行调试。首先，查看 Claude Code 的日志输出，通常包含 MCP 服务器的启动信息和错误信息。

```bash
# 启动 Claude Code 并查看详细日志
claude --verbose

# 单独测试 MCP 服务器
npx @modelcontextprotocol/server-filesystem /test/path
```

常见问题及解决方案包括：

1. **服务器启动失败**：检查 command 和 args 配置是否正确，确保相关 npm 包已安装或可通过 npx 获取。

2. **权限被拒绝**：检查文件系统权限，确保 Claude Code 有权访问配置中指定的目录和文件。

3. **环境变量未生效**：确认环境变量配置格式正确，敏感信息建议使用环境变量注入而非直接写在配置文件中。

4. **连接超时**：检查网络连接，某些 MCP 服务器需要访问外部 API 或服务。

---

## 4. Skill 使用指南

### 4.1 Skill 概念介绍

Skill 是 Claude Code 的任务专业化系统，它允许用户定义特定领域或任务的专业知识和工作流程。与 MCP 侧重于外部工具和数据访问不同，Skill 更侧重于定义"如何做某件事"的方法论和步骤。

Skill 本质上是一组预定义的指令和模板，当用户触发某个 Skill 时，Claude Code 会加载相应的指令，按照定义的流程执行任务。这使得 Claude Code 在处理特定类型任务时能够表现出更高的专业性和一致性。

Skill 的典型应用场景包括：代码审查、文档生成、测试编写、API 设计、安全审计等。通过定义专门的 Skill，可以确保这些任务每次都按照相同的最佳实践标准执行，减少人为疏漏。

### 4.2 内置 Skill 使用

Claude Code 提供了一些内置 Skill，用户可以直接使用。这些内置 Skill 覆盖了常见的开发任务场景。

#### 4.2.1 代码审查 Skill

代码审查 Skill 用于对代码变更进行全面的质量检查，包括代码风格、潜在 bug、安全漏洞、性能问题等方面。

```
# 使用代码审查 Skill
/review

# 审查特定文件
/review src/components/Button.tsx

# 审查最近的提交
/review --git HEAD~1
```

#### 4.2.2 测试生成 Skill

测试生成 Skill 可以根据代码自动生成相应的测试用例，支持多种测试框架。

```
# 为当前文件生成测试
/test src/utils/format.ts

# 生成特定类型的测试
/test --type unit src/lib/calculator.ts
/test --type integration src/api/users.ts
```

### 4.3 Skill 调用方式

Skill 的调用主要有以下几种方式，根据具体场景选择最合适的方式。

#### 4.3.1 斜杠命令方式

在 Claude Code 对话中，使用斜杠命令是最直接的 Skill 调用方式。只需输入 `/skill-name` 即可触发相应的 Skill。

```
/my-skill
/custom-review
/api-design
```

#### 4.3.2 自动触发方式

某些 Skill 可以配置为在特定条件下自动触发。例如，当检测到特定类型的文件变更时自动运行代码审查，或者在创建新文件时自动应用模板。

#### 4.3.3 对话中引用方式

在与 Claude Code 的对话中，可以通过描述来请求使用特定的 Skill。

```
请使用代码审查 Skill 检查我最近的改动
```

---

## 5. 自定义 Skill 规范

### 5.1 Skill 文件结构

自定义 Skill 可以以单文件形式或目录形式组织。对于简单的 Skill，单文件形式更加简洁；对于复杂的 Skill，目录形式便于组织多个相关文件和资源。

#### 5.1.1 单文件形式

单文件 Skill 以 `.md` 或 `.mdc` 为扩展名，放置在 `.claude/skills/` 目录下。文件内容遵循特定的格式规范。

```
.claude/
└── skills/
    ├── code-review.md
    ├── api-design.md
    └── test-generator.md
```

#### 5.1.2 目录形式

目录形式的 Skill 允许包含更多资源文件，如模板、示例代码、配置文件等。

```
.claude/
└── skills/
    └── my-skill/
        ├── skill.md          # 主配置文件（必需）
        ├── templates/        # 模板文件目录
        │   ├── component.tsx.tpl
        │   └── test.ts.tpl
        ├── examples/         # 示例文件目录
        │   └── example.ts
        └── config.json       # 额外配置（可选）
```

### 5.2 Skill 配置文件格式

Skill 的主配置文件（skill.md）是核心，它定义了 Skill 的元信息和具体指令内容。

```markdown
---
name: my-custom-skill
description: 这是一个自定义 Skill 的描述，说明其用途和功能
triggers:
  - /my-skill
  - /ms
globs:
  - "**/*.ts"
  - "**/*.tsx"
---

# My Custom Skill

## 角色定义
你是一个专业的 [领域] 专家，擅长 [具体能力]。

## 任务目标
当用户调用此 Skill 时，你需要完成以下任务：
1. [任务步骤1]
2. [任务步骤2]
3. [任务步骤3]

## 工作流程

### 第一步：[阶段名称]
详细描述这一阶段需要做什么，包括：
- 需要收集哪些信息
- 需要分析哪些内容
- 需要做出哪些判断

### 第二步：[阶段名称]
...

## 输出规范
输出的内容应该遵循以下格式：
- 格式要求
- 结构要求
- 质量标准

## 注意事项
- 重要提醒1
- 重要提醒2
- 需要避免的错误
```

### 5.3 元数据字段说明

Skill 配置文件的 Front Matter 部分支持以下元数据字段：

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| name | string | 是 | Skill 的唯一标识名称 |
| description | string | 是 | Skill 的功能描述 |
| triggers | string[] | 否 | 触发命令别名列表 |
| globs | string[] | 否 | 关联的文件模式列表 |
| version | string | 否 | Skill 版本号 |
| author | string | 否 | 作者信息 |
| tags | string[] | 否 | 标签列表，便于分类和搜索 |

### 5.4 最佳实践

在编写自定义 Skill 时，遵循以下最佳实践可以确保 Skill 的质量和可用性。

#### 5.4.1 清晰的角色定义

Skill 应该明确定义 Claude 在执行该 Skill 时扮演的角色。这有助于 Claude 调整其输出风格和专业深度。

```markdown
## 角色定义
你是一个资深的前端架构师，拥有 10 年以上的 React 开发经验。你精通组件设计模式、
状态管理、性能优化和可访问性标准。在代码审查时，你会从架构可扩展性、性能影响、
维护成本等多个维度进行评估。
```

#### 5.4.2 结构化的工作流程

将复杂任务分解为清晰的步骤，每个步骤有明确的目标和输出要求。这有助于保证任务执行的完整性和一致性。

```markdown
## 工作流程

### 第一阶段：需求分析
1. 阅读并理解用户的任务描述
2. 识别关键需求和约束条件
3. 确认是否有模糊或遗漏的信息
4. 如有需要，向用户提出澄清问题

### 第二阶段：方案设计
1. 分析多种可能的解决方案
2. 评估每种方案的优缺点
3. 选择最优方案并说明理由
4. 输出详细的设计文档

### 第三阶段：实施执行
1. 按照设计方案编写代码
2. 确保代码符合项目规范
3. 添加必要的注释和文档
4. 进行自我审查和测试
```

#### 5.4.3 具体的输出规范

明确定义输出格式和内容要求，使用户能够得到一致且高质量的结果。

```markdown
## 输出规范

### 代码输出要求
- 使用 TypeScript，提供完整的类型定义
- 遵循项目的 ESLint 和 Prettier 配置
- 所有公共接口添加 JSDoc 注释
- 复杂逻辑添加行内注释说明

### 文档输出格式
输出文档应包含以下部分：
1. **概述**：简要说明功能和用途（2-3 句话）
2. **设计思路**：解释设计决策和考量
3. **使用示例**：提供至少 2 个使用示例
4. **注意事项**：列出需要注意的限制和边界情况
```

---

## 6. 自定义 Skill 示例

### 6.1 示例一：API 设计 Skill

以下是一个完整的 API 设计 Skill 示例，用于指导 Claude Code 设计 RESTful API 接口。

```markdown
---
name: api-design
description: RESTful API 设计专家，帮助设计和文档化 API 接口
triggers:
  - /api
  - /api-design
globs:
  - "**/api/**/*.ts"
  - "**/routes/**/*.ts"
---

# API 设计专家

## 角色定义
你是一个 API 设计专家，精通 RESTful 架构风格和 API 设计最佳实践。
你熟悉 OpenAPI 3.0 规范，能够设计出易于使用、高性能、安全可靠的 API。

## 任务目标
帮助用户设计符合 RESTful 规范的 API 接口，包括：
- 资源和端点设计
- 请求/响应格式定义
- 错误处理规范
- API 文档生成

## 设计原则

### RESTful 规范
1. 使用名词而非动词表示资源
2. 使用 HTTP 方法表达操作语义
3. 使用复数形式表示资源集合
4. 使用嵌套表示资源关系
5. 保持 URL 层级简洁清晰

### HTTP 方法使用规范
- GET：获取资源，幂等操作
- POST：创建资源，非幂等
- PUT：完整更新资源，幂等
- PATCH：部分更新资源，幂等
- DELETE：删除资源，幂等

### 状态码使用规范
- 200：成功（GET、PUT、PATCH）
- 201：创建成功（POST）
- 204：成功无内容（DELETE）
- 400：请求参数错误
- 401：未认证
- 403：无权限
- 404：资源不存在
- 422：业务逻辑错误
- 500：服务器内部错误

## 输出模板

### API 定义格式
```typescript
/**
 * @api {method} /path 资源操作描述
 * @apiName 操作名称
 * @apiGroup 资源分组
 * 
 * @apiParam {Type} fieldName 参数说明
 * @apiSuccess {Type} fieldName 返回字段说明
 * @apiError {Type} fieldName 错误字段说明
 */
```

## 工作流程

### 第一步：需求理解
1. 分析用户描述的 API 需求
2. 识别涉及的资源类型
3. 确定资源之间的关系
4. 明确权限和安全要求

### 第二步：端点设计
1. 设计资源 URL 结构
2. 确定每个端点的 HTTP 方法
3. 设计请求参数和请求体
4. 设计响应格式

### 第三步：文档输出
1. 生成 API 接口文档
2. 提供请求/响应示例
3. 说明错误处理方式
4. 补充注意事项
```

### 6.2 示例二：代码审查 Skill

以下是一个代码审查 Skill 示例，用于对代码进行全面的质量检查。

```markdown
---
name: code-review
description: 专业代码审查，检查代码质量、安全性和性能问题
triggers:
  - /review
  - /code-review
---

# 代码审查专家

## 角色定义
你是一个资深的代码审查专家，拥有丰富的代码质量把控经验。
你会从代码正确性、可读性、性能、安全性、可维护性等多个维度进行审查，
并提供具体的改进建议。

## 审查维度

### 1. 代码正确性
- 逻辑是否正确，边界条件是否处理
- 异常情况是否考虑周全
- 数据类型使用是否恰当
- 并发场景是否安全

### 2. 代码可读性
- 命名是否清晰、有意义
- 代码结构是否清晰
- 注释是否充分且准确
- 是否遵循项目编码规范

### 3. 性能考量
- 是否存在明显的性能问题
- 算法复杂度是否合理
- 是否有不必要的计算或 I/O
- 内存使用是否高效

### 4. 安全性检查
- 是否存在注入风险
- 敏感数据是否妥善处理
- 权限控制是否完备
- 依赖包是否有已知漏洞

### 5. 可维护性
- 代码是否易于修改和扩展
- 模块划分是否合理
- 是否存在重复代码
- 测试覆盖是否充分

## 输出格式

### 审查报告模板

## 代码审查报告

### 概述
- 审查文件：[文件路径]
- 审查范围：[具体范围或变更]
- 总体评价：[优秀/良好/需改进/需重写]

### 问题列表

#### 🔴 严重问题（必须修复）
| 行号 | 问题描述 | 建议修改 |
|------|----------|----------|
| xx | 问题描述 | 修改建议 |

#### 🟡 中等问题（建议修复）
| 行号 | 问题描述 | 建议修改 |
|------|----------|----------|
| xx | 问题描述 | 修改建议 |

#### 🟢 轻微问题（可选修复）
| 行号 | 问题描述 | 建议修改 |
|------|----------|----------|
| xx | 问题描述 | 修改建议 |

### 亮点
- [代码亮点1]
- [代码亮点2]

### 改进建议
1. [总体建议1]
2. [总体建议2]
```

### 6.3 示例三：测试生成 Skill

以下是一个测试用例生成 Skill 示例，用于自动生成单元测试。

```markdown
---
name: test-generator
description: 自动生成单元测试用例，支持 Jest 和 Vitest
triggers:
  - /test
  - /gen-test
globs:
  - "**/*.ts"
  - "**/*.tsx"
---

# 测试生成专家

## 角色定义
你是一个测试开发工程师，精通单元测试、集成测试和端到端测试。
你熟悉测试驱动开发（TDD）理念，能够编写高质量、高覆盖率的测试用例。

## 测试原则

### AAA 模式
- Arrange（准备）：设置测试前置条件
- Act（执行）：执行被测试的代码
- Assert（断言）：验证执行结果

### 测试覆盖要求
- 正常路径：主要功能的正常执行
- 边界条件：空值、最大值、最小值等
- 异常情况：错误输入、异常抛出
- 并发场景：多线程、竞态条件

### 测试命名规范
```typescript
describe('功能模块名称', () => {
  describe('方法/函数名称', () => {
    it('应该在[条件]下[期望行为]', () => {
      // 测试代码
    });
  });
});
```

## 输出模板

### 单元测试模板
```typescript
import { describe, it, expect, beforeEach, afterEach, vi } from 'vitest';
import { FunctionName } from './module';

describe('FunctionName', () => {
  describe('正常情况', () => {
    it('应该正确处理有效输入', () => {
      // Arrange
      const input = 'valid input';
      const expected = 'expected output';
      
      // Act
      const result = FunctionName(input);
      
      // Assert
      expect(result).toBe(expected);
    });
  });

  describe('边界条件', () => {
    it('应该正确处理空输入', () => {
      expect(() => FunctionName('')).toThrow();
    });
  });

  describe('异常情况', () => {
    it('应该正确处理无效输入', () => {
      expect(() => FunctionName(null)).toThrow(TypeError);
    });
  });
});
```

## 工作流程

### 第一步：代码分析
1. 读取并分析目标代码
2. 识别公共接口和关键函数
3. 理解函数的输入输出和副作用
4. 识别关键逻辑分支

### 第二步：测试设计
1. 为每个公共方法设计测试用例
2. 覆盖所有逻辑分支
3. 设计边界条件测试
4. 设计异常情况测试

### 第三步：测试生成
1. 生成完整的测试文件
2. 添加必要的 mock 和 stub
3. 确保测试可运行
4. 输出覆盖率预估
```

---

## 7. 使用技巧与最佳实践

### 7.1 提示词技巧

与 Claude Code 交互时，使用有效的提示词技巧可以显著提高交互效率和结果质量。

#### 7.1.1 明确上下文

提供充分的上下文信息，帮助 Claude 理解任务背景。避免过于简短的指令，而是提供完整的需求描述。

```
# 不推荐
修复这个 bug

# 推荐
在 src/components/UserList.tsx 中，当用户列表为空时，页面显示空白。
请修复这个问题，添加空状态提示组件，显示"暂无用户数据"的提示信息。
```

#### 7.1.2 分步骤指导

对于复杂任务，将其分解为多个步骤，逐步指导 Claude 完成任务。

```
请帮我完成以下任务：
1. 首先分析 src/api/ 目录下的现有 API 结构
2. 然后设计新的用户管理 API 接口
3. 实现接口代码
4. 编写对应的测试用例
5. 更新 API 文档
```

#### 7.1.3 提供示例

提供期望输出的示例，帮助 Claude 理解预期的格式和风格。

```
请按照以下格式输出组件文档：

## Button 组件

### 用途
通用按钮组件，用于触发用户操作。

### 属性
| 属性名 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| type | 'primary' \| 'secondary' | 'primary' | 按钮类型 |

### 使用示例
\`\`\`tsx
<Button type="primary">点击我</Button>
\`\`\`
```

### 7.2 项目管理技巧

#### 7.2.1 维护 CLAUDE.md

定期更新 `CLAUDE.md` 文件，确保 Claude 始终了解项目的最新状态和约定。这个文件应该包含项目的核心技术栈、编码规范、目录结构说明，以及任何特殊的注意事项或约定。

当项目结构发生变化、引入新技术、或修改编码规范时，都应该同步更新 `CLAUDE.md` 文件。这可以确保 Claude Code 的建议始终与项目实际情况保持一致。

#### 7.2.2 合理配置权限

根据项目需求和安全考虑，合理配置 Claude Code 的权限。对于生产环境或敏感项目，应该采用最小权限原则，只授予必要的操作权限。

```json
{
  "permissions": {
    "allow": [
      "Read(src/**)",
      "Edit(src/**)",
      "Bash(npm run lint)",
      "Bash(npm test)"
    ],
    "deny": [
      "Read(.env*)",
      "Read(secrets/**)",
      "Bash(npm publish)",
      "Bash(git push *)"
    ]
  }
}
```

#### 7.2.3 版本控制集成

充分利用 Claude Code 与 Git 的集成能力。在执行重要修改前，可以先让 Claude 查看当前的 git 状态和最近的提交历史，帮助其更好地理解变更上下文。

```bash
# 在对话中请求 git 相关操作
查看最近的提交记录
分析当前分支与 main 分支的差异
帮我写一个清晰的 commit message
```

### 7.3 效率提升技巧

#### 7.3.1 使用自定义命令

将常用的操作定义为自定义命令，减少重复输入。在 `.claude/commands/` 目录下创建命令文件。

```markdown
<!-- .claude/commands/deploy.md -->
# 部署命令

请执行以下部署步骤：
1. 运行所有测试，确保通过
2. 执行代码检查，确保无错误
3. 构建生产版本
4. 检查构建产物
5. 提供部署命令建议
```

使用时只需输入 `/deploy` 即可触发。

#### 7.3.2 利用模板生成

为常见文件类型创建模板，如组件、API 路由、测试文件等。当需要创建新文件时，Claude Code 会自动应用相应的模板。

```markdown
<!-- .claude/templates/component.md -->
# React 组件模板

创建新的 React 组件时，请遵循以下结构：
1. 组件文件：使用 TypeScript 和函数组件
2. 样式文件：使用 CSS Modules 或 Tailwind
3. 测试文件：使用 Vitest 和 Testing Library
4. 索引文件：导出组件及其类型
```

#### 7.3.3 批量操作处理

对于需要批量处理的任务，如批量重命名、批量格式化等，可以向 Claude Code 提供完整的操作列表，一次性执行完成。

```
请帮我完成以下批量操作：
1. 将 src/components/ 下所有组件文件重命名为 PascalCase
2. 更新所有组件的导入路径
3. 确保所有导出文件 index.ts 同步更新
```

### 7.4 常见问题解决

#### 7.4.1 上下文丢失问题

当处理大型项目或长时间对话时，可能会遇到上下文丢失的情况。解决方案包括：

- 在关键节点总结当前进展和下一步计划
- 使用 CLAUDE.md 记录重要的项目信息
- 将复杂任务分解为多个独立会话
- 定期重新说明任务背景

#### 7.4.2 权限拒绝问题

如果遇到权限被拒绝的错误，可以通过以下方式解决：

- 检查当前权限配置，确认操作是否在允许列表中
- 临时提升权限以完成特定操作
- 在配置文件中添加相应的权限规则
- 使用交互模式，在执行时确认操作

#### 7.4.3 MCP 连接问题

MCP 服务器连接失败时，可以尝试：

- 检查服务器配置是否正确
- 确认相关依赖是否已安装
- 查看 Claude Code 日志获取详细错误信息
- 单独测试 MCP 服务器是否正常工作

---

## 附录

### A. 常用命令速查

| 命令 | 说明 |
|------|------|
| `claude` | 启动 Claude Code |
| `claude --version` | 查看版本 |
| `claude --help` | 查看帮助 |
| `/skill-name` | 调用 Skill |
| `/help` | 查看可用命令 |
| `/clear` | 清除对话 |
| `/config` | 打开配置 |

### B. 配置文件位置

| 文件 | 位置 |
|------|------|
| 全局配置 | `~/.config/claude-code/settings.json` |
| MCP 配置 | `~/.config/claude-code/mcp.json` 或 `.mcp.json` |
| 项目配置 | `项目根目录/CLAUDE.md` |
| Skill 定义 | `项目根目录/.claude/skills/` |
| 自定义命令 | `项目根目录/.claude/commands/` |

### C. 参考资源

- [Claude Code 官方文档](https://docs.anthropic.com/claude-code)
- [MCP 协议规范](https://modelcontextprotocol.io/)
- [Anthropic API 文档](https://docs.anthropic.com/)
- [MCP 服务器列表](https://github.com/modelcontextprotocol/servers)
