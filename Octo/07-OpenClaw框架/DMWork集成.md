---
title: DMWork 集成
type: guide
project: openclaw
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, openclaw, dmwork, channel, wukongim]
---

# DMWork 集成

[[概述|← 返回概述]]

DMWork 是基于 WuKongIM 的私有 IM 平台。本文档描述 DMWork 如何与 OpenClaw 集成。

## 当前集成状态

DMWork 目前以 **Skill 形式**接入 OpenClaw（而非完整 Channel Plugin），通过 `~/.openclaw/skills/dmwork/SKILL.md` 教会 Agent 如何使用 DMWork Bot API。

| 能力 | 状态 | 说明 |
|------|------|------|
| Agent 主动发送消息 | ✅ | 通过 Bot API 调用 |
| 群聊 @mention 发送 | ✅ | 支持 `@名字` 格式 |
| 被动接收消息触发 Agent | ❌ | 需要完整 Channel Plugin |
| 双向对话 | ❌ | 需要完整 Channel Plugin |

## Skill 集成方式

当前 Skill 方式：
```
OpenClaw Agent
  → 识别到需要发 DMWork 消息
  → 调用 dmwork skill 中的 HTTP API
  → DMWork Bot API（/v1/bot/*）
  → WuKongIM
  → 用户收到消息
```

适用于：Agent 需要**主动推送**消息到 DMWork 的场景（如通知、报告、任务结果推送）。

## 完整 Channel Plugin 集成（推荐，未来方向）

如需双向通信（用户在 DMWork 发消息 → Agent 响应），需要开发完整 Channel Extension：

### 目录结构

```
extensions/dmwork/
├── openclaw.plugin.json   # 声明为 channel 插件
├── package.json
├── index.ts               # 插件入口
└── src/
    ├── channel.ts         # ChannelPlugin 实现
    └── runtime.ts         # WuKongIM WebSocket 连接管理
```

### openclaw.plugin.json

```json
{
  "id": "dmwork",
  "channels": ["dmwork"],
  "configSchema": {
    "type": "object",
    "properties": {
      "apiUrl":   { "type": "string", "description": "DMWork API URL" },
      "wsUrl":    { "type": "string", "description": "WuKongIM WS URL" },
      "botToken": { "type": "string", "description": "Bot Token（bf_ 前缀）" }
    },
    "required": ["apiUrl", "botToken"]
  }
}
```

### 配置示例

```json5
{
  channels: {
    dmwork: {
      accounts: {
        "default": {
          apiUrl: "https://im.example.com",
          wsUrl: "wss://im.example.com",
          botToken: "bf_xxxxxxxxxxxxxxxx",
          requireMention: true,    // 群聊中需要 @bot 才触发
          historyLimit: 10         // 拉取历史消息数量
        }
      }
    }
  }
}
```

### 多 Bot 配置

```json5
{
  channels: {
    dmwork: {
      accounts: {
        "xijing": {
          botToken: "bf_xijing_token",
          requireMention: true
        },
        "main": {
          botToken: "bf_main_token",
          requireMention: false
        }
      }
    }
  }
}
```

### 关键实现点

**Session Key 格式**：
- 群聊：`agent:<agentId>:dmwork:group:<groupNo>`
- 私聊：`agent:<agentId>:dmwork:direct:<uid>`

**@mention 解析**：
识别 `@名字` 格式，过滤出对 Bot 的 mention，触发 Agent 处理。

**消息格式**：
WuKongIM 消息体需要序列化/反序列化为 OpenClaw 标准格式。

**认证**：
Bot Token 存储在 `channels.dmwork.accounts.<accountId>.botToken`。

**WuKongIM 协议参考**：
见 [[dmwork-adapters 文档|project_dmwork_adapters]]，WebSocket 协议、CONNACK 握手、心跳机制等。

## Bindings 配置

当 Channel Plugin 就绪后，配置路由规则：

```json5
{
  bindings: [
    // xijing agent 处理特定用户的私聊
    {
      agentId: "xijing",
      match: {
        channel: "dmwork",
        peer: { kind: "direct", id: "merlin_uid" }
      }
    },
    // 所有 DMWork 群聊交给 main agent
    {
      agentId: "main",
      match: { channel: "dmwork", peer: { kind: "group" } }
    }
  ]
}
```

## 相关文档

- [[Channel系统]] — ChannelPlugin 接口
- [[Bot-API]] — DMWork Bot API 完整参考
- [[Session管理]] — Session key 格式

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿，明确当前 Skill 集成与未来 Channel Plugin 集成的区别 |
