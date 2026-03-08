开始使用claudecode

## 创建目录，进入目录

```
mkdir study
cd study
claude --verbose  #查看运行日志
```

执行 /init

## 创建第一个skill

| 位置 | 路径                                                         | 适用于               |
| :--- | :----------------------------------------------------------- | :------------------- |
| 企业 | 参阅[托管设置](https://code.claude.com/docs/zh-CN/settings#settings-files) | 你的组织中的所有用户 |
| 个人 | `~/.claude/skills/<skill-name>/SKILL.md`                     | 你的所有项目         |
| 项目 | `.claude/skills/<skill-name>/SKILL.md`                       | 仅此项目             |
| 插件 | `<plugin>/skills/<skill-name>/SKILL.md`                      | 启用插件的位置       |

在当前目录，创建一个claude code的日志skill，能自动监听agent的执行，打印agent运行状态和结果，日志输出到一个文件，文件名包含session信息。

```
.claude
|__ skills/
|     ├── logger/  #skill目录
│        ├── SKILL.md
│        ├── reference.md (可选)
│        └── scripts/ (可选)
|__ hooks
|    ├── agent_logger.py
|__ settings.json   #配置文件
```

SKILL.md:

````
---
name: logger
description: Auto-log Claude Code Agent tool execution. Use when you need to capture sub-agent start/end status, result summary, and session-scoped logs to files via hooks.

---

# Logger Skill

Configure Claude Code hooks so every `Agent` tool call is logged automatically.

## Behavior

- Listen to `PreToolUse` for `Agent`: record start status and input preview.
- Listen to `PostToolUse` for `Agent`: record completion status and result preview.
- Write one line per event to `.claude/logs/<session_id>.log`.
- If session id is missing, fall back to `unknown-session`.

## Log Line Format

```
<timestamp> [LEVEL] [session:<session_id>] [agent:<agent_label>] <message>  {json_context}
```

## Required Files

- Hook config: `.claude/settings.json`
- Hook script: `.claude/hooks/agent_logger.py`
````

agent_logger.py

```python
#!/usr/bin/env python3
import datetime as dt
import json
import os
import sys
from pathlib import Path

MAX_PREVIEW = 240


def _safe_json_load(raw: str):
    if not raw.strip():
        return {}
    try:
        return json.loads(raw)
    except Exception:
        return {"_raw": raw[:MAX_PREVIEW]}


def _preview(value):
    if value is None:
        return ""
    if isinstance(value, str):
        text = value
    else:
        try:
            text = json.dumps(value, ensure_ascii=False)
        except Exception:
            text = str(value)
    text = " ".join(text.split())
    if len(text) > MAX_PREVIEW:
        return text[: MAX_PREVIEW - 3] + "..."
    return text


def _pick(payload, *keys, default=None):
    for key in keys:
        if isinstance(payload, dict) and key in payload:
            return payload.get(key)
    return default


def _session_id(payload):
    return (
        _pick(payload, "session_id", "sessionId")
        or os.getenv("CLAUDE_SESSION_ID")
        or "unknown-session"
    )


def _agent_label(payload):
    return (
        _pick(payload, "agent_label", "agentLabel")
        or _pick(payload, "agent", "tool_name", "toolName")
        or "main"
    )


def _event_name(payload):
    return _pick(payload, "hook_event_name", "event", "eventName", default="UnknownEvent")


def _tool_input(payload):
    ti = _pick(payload, "tool_input", "toolInput", default={})
    return ti if isinstance(ti, dict) else {"value": ti}


def _tool_response(payload):
    tr = _pick(payload, "tool_response", "toolResponse", default={})
    return tr if isinstance(tr, dict) else {"value": tr}


def _build_message(payload):
    event_name = _event_name(payload)
    tool_input = _tool_input(payload)
    tool_response = _tool_response(payload)

    if event_name == "PreToolUse":
        msg = f"Agent started: {_preview(tool_input.get('description') or tool_input.get('prompt') or '')}"
        ctx = {
            "event": event_name,
            "tool": _pick(payload, "tool_name", "toolName", default="Agent"),
            "input_preview": _preview(tool_input),
        }
        level = "INFO"
    elif event_name == "PostToolUse":
        msg = "Agent finished"
        ctx = {
            "event": event_name,
            "tool": _pick(payload, "tool_name", "toolName", default="Agent"),
            "result_preview": _preview(tool_response),
        }
        level = "INFO"
    else:
        msg = f"Agent event: {event_name}"
        ctx = {
            "event": event_name,
            "tool": _pick(payload, "tool_name", "toolName", default="Agent"),
            "payload_preview": _preview(payload),
        }
        level = "DEBUG"

    return level, msg, ctx


def _log_path(session_id: str) -> Path:
    root = Path(os.getenv("CLAUDE_PROJECT_DIR", "."))
    log_dir = root / ".claude" / "logs"
    log_dir.mkdir(parents=True, exist_ok=True)
    return log_dir / f"{session_id}.log"


def main():
    payload = _safe_json_load(sys.stdin.read())
    session_id = _session_id(payload)
    agent = _agent_label(payload)
    level, msg, ctx = _build_message(payload)

    ts = dt.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    line = (
        f"{ts} [{level}] [session:{session_id}] [agent:{agent}] {msg}  "
        f"{json.dumps(ctx, ensure_ascii=False)}"
    )

    print(line, file=sys.stderr)

    try:
        with _log_path(session_id).open("a", encoding="utf-8") as f:
            f.write(line + "\n")
    except Exception as exc:
        print(f"agent_logger write failed: {exc}", file=sys.stderr)


if __name__ == "__main__":
    main()
```

settings.json:

```
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Agent",
        "hooks": [
          {
            "type": "command",
            "command": "python \".claude/hooks/agent_logger.py\""
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Agent",
        "hooks": [
          {
            "type": "command",
            "command": "python \".claude/hooks/agent_logger.py\""
          }
        ]
      }
    ]
  }
}
```

测试验证：

```
1. 调用 skill（让 Claude 遵循日志规范）    /logger
2. Hook 是全自动的，但是需要配置
```

  ┌──────────┬───────────────────────────┬─────────────────────────────┐
  │                          │       logging skill                                            │                 agent_logger hook                           │
  ├──────────┼───────────────────────────┼─────────────────────────────┤
  │ 触发方式           │    手动 /logger                                             │                          全自动                                     │
  ├──────────┼───────────────────────────┼─────────────────────────────┤
  │    作用对象       │             Claude 自己输出的文本格式            │                  Agent 工具调用事件                        │
  ├──────────┼───────────────────────────┼─────────────────────────────┤
  │ 日志内容          │            Claude 在回复中写的日志行             │                agent 启动/完成/失败事件                  │
  ├──────────┼───────────────────────────┼─────────────────────────────┤
  │ 输出位置          │ 对话流                                                         │               stderr + .claude/logs/ 文件               ─┤
 └──────────┴───────────────────────────┴─────────────────────────────┘

通常只需要 hook，不需要手动调 skill。如果你想让 Claude 在回复中也输出结构化日志（比如调试复杂任务），才需要 /logger



## Hook与Skill

### 1.Hook 直接调用 Skill 脚本（推荐）

```
logger/
    SKILL.md
    log.py
```

Hook 示例：.claude/hooks/pre-tool-use.py

```
#!/usr/bin/env python3
STEP="$1"
.claude/skills/logger/log.py "tool_start: $STEP"
```

### 2.Hook 调用 Claude 触发 Skill

.claude/hooks/pre-tool-use.py

```
#!/usr/bin/env python3

TASK_OUTPUT="$1"
claude <<EOF
Use the agent-logger skill to record this result:

$TASK_OUTPUT
EOF
```

