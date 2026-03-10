# 飞书 配置说明



## 飞书渠道配置

飞书推荐使用 `WebSocket` 长连接模式，这样不需要公网 webhook 地址。

### 最小可用配置

把下面内容加到 `~/.openclaw/openclaw.json` 中：

```json5
{
  env: {
    ANTHROPIC_API_KEY: "sk-ant-..."
  },

  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-opus-4-6"
      }
    }
  },

  channels: {
    feishu: {
      enabled: true,
      connectionMode: "websocket",
      domain: "feishu",
      dmPolicy: "pairing",
      groupPolicy: "open",
      accounts: {
        main: {
          appId: "cli_xxx",
          appSecret: "xxx",
          botName: "OpenClaw Bot"
        }
      }
    }
  }
}
```

### 飞书国际版 / Lark

如果使用的是国际版 Lark，把：

```json5
domain: "feishu"
```

改成：

```json5
domain: "lark"
```

## 飞书开放平台侧需要完成的配置

在飞书开放平台创建企业自建应用后，至少需要完成以下步骤：

1. 获取 `App ID`
2. 获取 `App Secret`
3. 开启 Bot 能力
4. 事件订阅选择 `WebSocket / 长连接`
5. 添加消息接收事件 `im.message.receive_v1`
6. 发布应用

如果没有启用对应事件或权限，机器人在飞书中可能不会收到消息。

## 飞书常见配置项说明

下面这些是后续经常会调整的配置：

- `channels.feishu.enabled`
  - 是否启用飞书渠道
- `channels.feishu.domain`
  - 取值一般为 `feishu` 或 `lark`
- `channels.feishu.connectionMode`
  - 建议使用 `websocket`
- `channels.feishu.dmPolicy`
  - 私聊处理策略
- `channels.feishu.groupPolicy`
  - 群聊处理策略
- `channels.feishu.allowFrom`
  - 私聊白名单
- `channels.feishu.groupAllowFrom`
  - 群聊白名单
- `channels.feishu.groups.<chat_id>.requireMention`
  - 群消息是否要求必须 `@机器人`
- `channels.feishu.streaming`
  - 是否流式输出
- `channels.feishu.blockStreaming`
  - 是否按块输出流式消息
- `channels.feishu.textChunkLimit`
  - 单条消息的文本分段长度
- `channels.feishu.accounts.main.appId`
  - 飞书应用 ID
- `channels.feishu.accounts.main.appSecret`
  - 飞书应用密钥

