## 安装CLI 

```
npm install -g @openai/codex
```



## 初始配置

配置文件：

```
~/.codex/config.toml
```

推荐配置：

```
model = "gpt-5.2-codex"
reasoning_effort = "high"
approval_mode = "on-failure"
sandbox_mode = "workspace-write"
```

参数解释：

| 参数             | 作用             |
| ---------------- | ---------------- |
| reasoning_effort | 推理深度         |
| approval_mode    | 是否需要你批准   |
| sandbox_mode     | 是否允许修改代码 |

建议：

```
reasoning_effort = high
approval_mode = on-failure
```



## Skill

默认可能没开 skills。

```
codex --enable skills
```

### 常用的 Codex Skills

 Skill = prompt + scripts + tools + workflow。 

```
codex install skill plantuml-architect grpc-helper openapi-spec securecode-scanner
```



## 多 Agent 协作





## Prompt

最推荐的 6 个 Prompt

### 1 architecture review

```
Act as a software architect.
Analyze this repository and identify:
- architectural issues
- coupling problems
- scalability risks
```

------

### 2 design system

```
Design a scalable architecture for:

Requirements:
- 10M users
- event driven
- microservices
- Kubernetes
```

------

### 3 refactor

```
Refactor this module with clean architecture principles.
```

------

### 4 tech debt

```
Identify technical debt and propose refactoring steps.
```

------

### 5 dependency analysis

```
generate a dependency graph of this project
```

------

### 6 performance

```
analyze performance bottlenecks in this service
```

### 7 自动 Code Review

```
Review this pull request as a senior engineer.

Focus on:
- architecture consistency
- performance issues
- security risks
- maintainability
```



## workflow

```
1 让 Codex 分析 repo
2 生成 architecture report
3 提出 refactor plan
4 自动改代码
5 自动跑测试
6 自动 PR
```

