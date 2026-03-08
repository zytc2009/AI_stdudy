下面是一份 **企业级 OpenClaw Skill 开发完整指南**。
这部分通常是企业内部文档里 **最核心的一章**，因为 Skill 决定了 Agent 能做什么。

该指南包含：

1. Skill 架构
2. Skill 目录结构
3. Skill Manifest
4. Skill 生命周期
5. Skill 开发规范
6. Skill 示例（完整）
7. Skill 调试
8. Skill 发布
9. 企业级最佳实践

------

# OpenClaw Skill 开发完整指南

版本：v1.0
适用对象：AI / Agent 开发工程师

------

# 1 Skill 概述

## 1.1 什么是 Skill

Skill 是 OpenClaw 的 **能力扩展模块**，用于让 AI Agent 调用外部工具或系统。

Skill 可以：

- 调用 API
- 执行 Shell 命令
- 操作数据库
- 处理文件
- 调用第三方服务

简单理解：

```
LLM = 大脑
Skill = 手脚
```

没有 Skill，大模型只能 **生成文本**。
有了 Skill，Agent 才能 **执行任务**。

------

# 2 Skill 架构

OpenClaw Skill 系统架构：

```
User Prompt
     │
     ▼
Agent Planner
     │
     ▼
Skill Selector
     │
     ▼
Skill Runtime
     │
     ▼
External Tool / API
```

Skill 在系统中的位置：

```
OpenClaw
│
├─ Agent
│
├─ Model
│
├─ Skill System
│   ├─ Skill Loader
│   ├─ Skill Registry
│   └─ Skill Executor
│
└─ Memory
```

------

# 3 Skill 类型

OpenClaw 支持多种 Skill 类型。

| 类型              | 示例           |
| ----------------- | -------------- |
| Tool Skill        | 执行系统命令   |
| API Skill         | 调用 HTTP API  |
| Data Skill        | 数据处理       |
| Integration Skill | GitHub / Slack |
| Workflow Skill    | 多步骤任务     |

------

# 4 Skill 目录结构

推荐 Skill 结构：

```
skills
│
├── github_skill
│   ├── skill.py
│   ├── manifest.yaml
│   └── README.md
│
├── web_search
│
└── file_manager
```

一个 Skill 最少包含：

```
manifest.yaml
skill.py
```

------

# 5 Skill Manifest

Manifest 描述 Skill 的元数据。

示例：

```yaml
name: github
description: interact with github repository
entry: skill.py
version: 1.0
author: AI Team
```

字段说明：

| 字段        | 说明       |
| ----------- | ---------- |
| name        | Skill 名称 |
| description | Skill 描述 |
| entry       | 入口文件   |
| version     | 版本       |
| author      | 作者       |

------

# 6 Skill 生命周期

Skill 在系统中的生命周期：

```
Load
 ↓
Register
 ↓
Invoke
 ↓
Execute
 ↓
Return Result
```

流程：

1. Skill Loader 扫描目录
2. 读取 manifest
3. 注册到 Skill Registry
4. Agent 调用 Skill
5. 执行 skill.run()

------

# 7 Skill 开发流程

标准开发流程：

```
创建 Skill
 ↓
编写 Manifest
 ↓
编写 Skill 代码
 ↓
本地测试
 ↓
注册 Skill
 ↓
Agent 调用
```

------

# 8 Skill 示例（完整）

下面是一个 **完整 Skill 示例：Web Search**。

目录：

```
skills/web_search
```

------

## manifest.yaml

```yaml
name: web_search
description: search information from web
entry: skill.py
version: 1.0
```

------

## skill.py

```python
import requests

class WebSearchSkill:

    def run(self, query: str):

        url = "https://api.duckduckgo.com"

        params = {
            "q": query,
            "format": "json"
        }

        response = requests.get(url, params=params)

        data = response.json()

        return data.get("AbstractText", "no result")
```

------

# 9 Tool Skill 示例

Shell 命令 Skill：

```
skills/shell
```

------

## manifest.yaml

```yaml
name: shell
description: run shell commands
entry: skill.py
```

------

## skill.py

```python
import subprocess

class ShellSkill:

    def run(self, command: str):

        result = subprocess.run(
            command,
            shell=True,
            capture_output=True,
            text=True
        )

        return result.stdout
```

------

# 10 API Skill 示例

GitHub Skill：

```python
import requests

class GithubSkill:

    def get_repo(self, repo):

        url = f"https://api.github.com/repos/{repo}"

        res = requests.get(url)

        return res.json()
```

------

# 11 Skill 注册

在配置文件注册：

```
~/.openclaw/config.yaml
```

示例：

```yaml
skills:
  - ~/.openclaw/skills/web_search
  - ~/.openclaw/skills/github
```

------

# 12 Skill 调试

推荐调试方式：

### 方法1：直接运行

```python
skill = WebSearchSkill()

print(skill.run("OpenAI"))
```

------

### 方法2：单元测试

创建：

```
tests/test_skill.py
```

示例：

```python
from web_search.skill import WebSearchSkill

def test_search():

    skill = WebSearchSkill()

    result = skill.run("python")

    assert result is not None
```

运行：

```
pytest
```

------

# 13 Skill 日志

推荐加入日志：

```python
import logging

logger = logging.getLogger(__name__)

logger.info("running skill")
```

------

# 14 Skill 错误处理

推荐模式：

```python
try:

    result = api_call()

except Exception as e:

    return str(e)
```

------

# 15 Skill 安全

必须避免：

```
eval()
exec()
```

Shell Skill 建议限制：

```
白名单命令
```

示例：

```python
allowed = ["ls","pwd","whoami"]
```

------

# 16 Skill 发布

推荐 Skill 仓库结构：

```
skills
│
├── openclaw-skill-github
├── openclaw-skill-websearch
└── openclaw-skill-shell
```

发布流程：

```
开发
 ↓
测试
 ↓
发布 GitHub
 ↓
团队安装
```

------

# 17 企业级 Skill 设计规范

推荐规则：

### 命名规范

```
openclaw-skill-xxx
```

示例：

```
openclaw-skill-github
openclaw-skill-database
```

------

### Skill 功能单一

不要一个 Skill 做太多事。

错误设计：

```
file + db + shell
```

正确设计：

```
file_skill
db_skill
shell_skill
```

------

### 输入输出标准化

输入：

```
JSON
```

输出：

```
JSON
```

示例：

```python
return {
    "status": "success",
    "data": result
}
```

------

# 18 Skill 性能优化

建议：

1. API 结果缓存
2. 减少重复请求
3. 异步调用

示例：

```python
async def run():
```

------

# 19 Skill Registry

系统维护 Skill Registry：

```
SkillRegistry
│
├── github
├── web_search
└── shell
```

Agent 通过 registry 调用：

```
registry.get("web_search")
```

------

# 20 Skill 最佳实践

企业项目建议：

### 1 Skill 单一职责

一个 Skill 只做一件事。

------

### 2 使用缓存

减少 API 调用。

------

### 3 加入日志

方便调试。

------

### 4 编写 README

说明 Skill 用法。

------

### 5 编写测试

保证稳定性。

------

# 21 Skill 示例（企业版）

企业 Skill 例子：

```
skills
│
├── github
├── jira
├── slack
├── mysql
├── redis
└── kubernetes
```

Agent 可以：

```
自动创建 PR
查询 Jira
发送 Slack 消息
部署服务
```

------

# 22 总结

Skill 是 OpenClaw **最核心的扩展机制**。

通过 Skill 可以实现：

- API 集成
- 系统自动化
- DevOps
- 数据处理
- Agent 工具调用

Skill 架构设计良好时，OpenClaw 可以成为 **企业级 AI 自动化平台**。