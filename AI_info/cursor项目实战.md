[toc]

# 核心目标：把 Cursor 当成一个“可持续协作的AI技术合伙人”

 让它完成：需求拆解 → 设计文档 → 架构设计 → 功能开发 → 进度记录 → 下次续跑。

# 一、核心思想：让 Cursor 进入「项目管理模式」

Cursor 默认是“对话模式”，
你要把它变成：

> 📁 基于仓库上下文
> 📄 基于固定文档结构
> 🔄 基于阶段推进
> 🧠 基于记忆文件

也就是说：

**不要靠对话记忆，而是靠工程文件记忆。**

# 二、推荐项目目录结构（必须建立）

在项目根目录创建：

```
/docs 长期记忆
    00_REQUIREMENT.md
    01_TASK_BREAKDOWN.md
    02_SYSTEM_DESIGN.md
    03_ARCHITECTURE.md
    04_PROGRESS.md
    05_TODO_NEXT.md

/prompts  标准指令模板
    requirement_prompt.md
    task_prompt.md
    design_prompt.md
    progress_prompt.md
    
./cursorrules  AI 行为规则
```

这是你的“AI大脑外置记忆”。

------

# 三、第一阶段：让它拆解需求

## 步骤 1：写清需求（00_REQUIREMENT.md）

示例：

```md
# 项目名称
AI 流媒体质量分析系统

# 项目目标
实时分析视频流质量：
- 卡顿检测
- 分辨率变化
- 黑屏检测
- 音视频不同步

# 技术栈
- C++
- FFmpeg
- gRPC
- AI 推理模块

# 性能要求
- 单流延迟 < 50ms
- 支持 100 路并发
```

------

## 步骤 2：在 Cursor 里输入

```
请基于 00_REQUIREMENT.md
将需求拆解成：
- 一级模块
- 二级子任务
- 技术风险点
- 优先级
- 可并行任务

输出到 01_TASK_BREAKDOWN.md
```

👉 让它“写入文件”，不要只输出聊天内容。

------

# 四、第二阶段：生成设计文档

等任务拆解完成后：

```
基于：
00_REQUIREMENT.md
01_TASK_BREAKDOWN.md

生成：
1. 系统总体设计
2. 模块间交互图（文字版）
3. 数据流设计
4. 核心类结构设计
5. 关键接口定义

输出到 02_SYSTEM_DESIGN.md
```

然后再执行：

```
基于 02_SYSTEM_DESIGN.md
生成项目目录结构和架构设计

输出到 03_ARCHITECTURE.md
```

------

# 五、第三阶段：按架构逐模块开发

关键指令模板：

```
我们现在开始开发模块：
[模块名]

请：
1. 阅读 03_ARCHITECTURE.md
2. 阅读 02_SYSTEM_DESIGN.md
3. 阅读 04_PROGRESS.md
4. 仅实现当前模块
5. 更新 04_PROGRESS.md
6. 如果未完成，更新 05_TODO_NEXT.md
```

这样就形成：

> Cursor 每次开发 = 读取历史文档 + 更新进度

------

# 六、如何实现“下次继续执行”

核心在：

### 04_PROGRESS.md 示例

```md
# 当前进度

## 已完成
- 视频解码模块
- 帧时间戳提取

## 开发中
- 卡顿检测算法（完成 60%）

## 未开始
- 黑屏检测
- 音视频同步分析
```

------

### 05_TODO_NEXT.md 示例

```md
# 下次开发指令

下一步：
1. 完成卡顿检测模块剩余逻辑
2. 增加单元测试
3. 验证边界条件

注意：
- 使用无锁队列
- 避免阻塞解码线程
```

------

## 下次打开 Cursor

直接输入：

```
阅读：
04_PROGRESS.md
05_TODO_NEXT.md
03_ARCHITECTURE.md

继续执行未完成任务。
```

它就会续跑。

------

# 七、让 Cursor 自动进入“项目经理模式”的终极 Prompt

你可以在项目根目录建一个：

```
/prompts/project_manager_mode.md
```

内容如下：

```
你现在是本项目的技术负责人。

规则：
1. 所有输出必须写入对应文档
2. 不重复输出聊天内容
3. 每次开发必须更新进度文件
4. 如果任务未完成，必须更新 TODO 文件
5. 严格按架构设计开发
6. 不擅自重构未授权模块
```

每次对话开头：

```
进入 project_manager_mode
```

------

# 八、进阶：使用 Cursor Rules（非常关键）

在 Cursor 中创建 `.cursorrules`：

```
- 优先读取 /docs 目录
- 所有设计必须文档化
- 所有新模块必须先设计再编码
- 修改代码必须说明影响范围
- 每次提交必须更新进度
```

这样它会自动遵守规则。

------

# 九、结合你做 AI + 流媒体 的进阶建议

你可以加入：

### 自动生成：

- PlantUML 架构图
- 时序图
- 类图
- API 文档
- 单元测试代码
- Benchmark 脚本

指令示例：

```
为当前模块生成：
- 类图（PlantUML）
- 单元测试代码
- Benchmark 测试
```

------

# 十、真正高级玩法：阶段锁定法

你可以限制 Cursor：

```
当前阶段：仅允许生成设计文档，不允许写代码。
```

等设计稳定再：

```
锁定设计文档，不允许修改，只能实现。
```

这样可以防止 AI 反复推翻自己。

------

# 十一、整体流程图

```
需求文档
   ↓
任务拆解
   ↓
系统设计
   ↓
架构设计
   ↓
模块开发
   ↓
进度记录
   ↓
下次继续
```

# 我开始构建自己的项目架构

## 项目架构

```
RtcStream/
│
├── CMakeLists.txt
├── README.md
│
├── docs/
│   ├── 00_REQUIREMENT.md
│   ├── 01_TASK_BREAKDOWN.md
│   ├── 02_SYSTEM_DESIGN.md
│   ├── 03_ARCHITECTURE.md
│   ├── 04_PROGRESS.md
│   ├── 05_TODO_NEXT.md
│   ├── 06_API_SPEC.md
│   └── 07_TEST_PLAN.md
│
├── prompts/
│   ├── project_manager_mode.md
│   ├── requirement_to_task.md
│   ├── design_generator.md
│   ├── architecture_generator.md
│   ├── module_dev.md
│   └── progress_update.md
│
├── src/
│   ├── common/
│   ├── decoder/
│   ├── analyzer/
│   ├── ai/
│   ├── transport/
│   ├── service/
│   └── main.cpp
│
├── include/
│
├── tests/
│
├── tools/
│
└── .cursorrules
```

## .cursorrules（必须放在根目录）

```
你是本项目的技术负责人。

规则：

1. 所有输出必须写入 docs 目录对应文件
2. 设计优先，禁止直接写代码
3. 未经允许不得修改已锁定设计
4. 每次实现功能必须：
   - 更新 04_PROGRESS.md
   - 更新 05_TODO_NEXT.md
5. 修改代码必须说明影响范围
6. 所有模块必须遵循架构分层
7. 不允许重复输出聊天解释
```

## 文档模板

ask -> PRD -> 设计文档 -> 开发计划(WBS) -> 构建 -> test -> 学习

对于团队来讲可能有分工：产品PRD， 研发(设计文档+code)， 测试(自动化)

```
产品：需求文档+原型(不限制格式，只需要描述和展示交互)
UX: 要打造统一的产品风格和交互方式，维护rules，借助AI来做设计
研发：平台层+跨平台 也需要做隔离，面向接口定设计
```



### docs/00_REQUIREMENT.md  

需求文档，可以根据自己的需求让AI生成

```
# 项目名称
AI 流媒体质量分析系统

# 项目目标
- 实时分析视频流质量
- 支持 100 路并发
- 延迟 < 50ms

# 功能需求
- 卡顿检测
- 黑屏检测
- 分辨率变化检测
- 音视频不同步检测
- AI 画质评分

# 技术栈
- C++
- FFmpeg
- gRPC
- ONNX Runtime

# 非功能要求
- 高性能
- 可扩展
- 模块化
```

### docs/04_PROGRESS.md

```
# 当前进度

## 已完成
-

## 开发中
-

## 未开始
-

## 技术债
-
```

### docs/05_TODO_NEXT.md

```
# 下次任务

-

# 注意事项

-
```

------

## Prompt 模板（可直接用）

### prompts/project_manager_mode.md

```
你现在是本项目的技术负责人。

执行流程：

1. 读取 docs 目录所有文件
2. 严格按阶段推进
3. 任何输出必须写入文件
4. 每次开发必须更新进度文件
5. 未完成必须写入 TODO 文件
```

## 第一步：需求拆解

在 Cursor 输入：

```
进入 project_manager_mode

基于 00_REQUIREMENT.md
生成：
- 一级模块
- 二级任务
- 技术风险
- 优先级
- 并行关系

输出到 01_TASK_BREAKDOWN.md
```

## 第二步：生成系统设计

```
基于 00_REQUIREMENT.md
基于 01_TASK_BREAKDOWN.md

生成：
- 系统分层设计
- 数据流
- 模块交互
- 类图（PlantUML）

输出到 02_SYSTEM_DESIGN.md
```

## 第三步：架构设计

```
基于 02_SYSTEM_DESIGN.md
生成：

- 目录结构
- 模块依赖图
- 核心接口定义
- 线程模型
- 内存管理策略

输出到 03_ARCHITECTURE.md
```

------

## 推荐架构分层

```
┌──────────────────────────┐
│ Service Layer            │
├──────────────────────────┤
│ Transport Layer (RTSP)   │
├──────────────────────────┤
│ Decode Layer (FFmpeg)    │
├──────────────────────────┤
│ Frame Buffer             │
├──────────────────────────┤
│ Analyzer Layer           │
│  ├── FreezeDetector      │
│  ├── BlackFrameDetector  │
│  ├── AVSyncDetector      │
│  └── AIQualityScorer     │
├──────────────────────────┤
│ Common Utils             │
└──────────────────────────┘
```

## 开发某个模块的标准指令

比如开发 FreezeDetector：

```
读取：
02_SYSTEM_DESIGN.md
03_ARCHITECTURE.md
04_PROGRESS.md

仅实现 FreezeDetector 模块：
- 头文件
- 实现文件
- 单元测试

更新 04_PROGRESS.md
如果未完成更新 05_TODO_NEXT.md
```

## 断点续跑方法

下次打开 Cursor：

```
进入 project_manager_mode

读取：
04_PROGRESS.md
05_TODO_NEXT.md
03_ARCHITECTURE.md

继续执行未完成任务
```

## 企业级增强玩法

你可以增加：

### 自动生成：

- Benchmark 工具
- 压测脚本
- Dockerfile
- CI 配置
- 性能 Profiling 方案
- gRPC proto 文件
- 插件式算法接口

## 进阶：锁定阶段模式

防止 AI 来回推翻设计：

```
当前阶段：仅允许设计文档，不允许写代码。
```

等设计稳定：

```
锁定 02_SYSTEM_DESIGN.md
仅允许实现代码。
```