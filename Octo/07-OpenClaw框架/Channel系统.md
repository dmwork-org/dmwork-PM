---
title: Channel 系统
type: reference
project: openclaw
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, openclaw, channel, plugin]
---

# Channel 系统

[[概述|← 返回概述]]

Channel 系统是 OpenClaw 将 20+ IM 平台统一为一套接口的核心抽象层。

## 设计原则

每个 Channel 是一个独立的 Extension（插件），通过 `openclaw.plugin.json` 声明自己实现了哪个 channel。

**Channel 数据流**：
```
inbound: IM平台消息 → Channel Plugin → Gateway → Session Manager → Agent
outbound: Agent → Session Manager → Gateway → Channel Plugin → IM平台
```

## ChannelPlugin 接口

每个 Channel 插件实现 `ChannelPlugin` 接口（来自 `openclaw/plugin-sdk`）：

| 接口方法 | 说明 |
|---------|------|
| `id` | Channel 唯一标识符 |
| `meta` | 名称、描述、图标等元数据 |
| `onboard()` | 引导用户完成账号配置的向导 |
| `pairing()` | DM 配对逻辑（pairing code 生成/验证） |
| `configManager` | 账号配置的读/写/验证 |
| `outbound()` | 将消息发送到 IM 平台 |
| `status()` | 健康检查、连接状态 |
| `probe()` | 连接探测 |

## startAccount：Channel 启动

Gateway 启动时，对每个**已配置的 Channel + Account** 调用 `startAccount`，建立到 IM 平台的长连接（WebSocket、Long Polling、HTTP Webhook 等）。

```
Gateway 启动
  ├── 读取 channels.whatsapp.accounts.default → startAccount("default")
  ├── 读取 channels.telegram.accounts.default → startAccount("default")
  └── 读取 channels.discord.accounts.default → startAccount("default")
       ...
```

## Inbound 流程（接收消息）

```
IM平台事件
  → Channel Plugin 解析（normalizeTarget、parsePeer）
  → 构建 InboundMessage envelope
  → Gateway 路由（查找匹配的 binding → agentId）
  → Session Manager（解析/创建 session key）
  → Agent 运行队列
  → Agent Loop（pi-mono 嵌入式运行时）
  → 生成回复
  → Outbound 流程
```

## Outbound 流程（发送消息）

```
Agent 生成回复
  → Session Manager（获取 last route）
  → Channel Plugin outbound（分片、格式化）
  → IM 平台 API 调用
  → 消息送达确认
```

## 多账号支持

每个 Channel 支持多个账号（`accountId`），例如同时登录两个 WhatsApp 号码：

```json5
{
  channels: {
    discord: {
      accounts: {
        "default": { token: "BOT_TOKEN_1" },
        "coding":  { token: "BOT_TOKEN_2" }
      }
    },
    whatsapp: {
      accounts: {
        "personal": { ... },
        "work":     { ... }
      }
    }
  }
}
```

## DM 安全策略

每个 Channel 有自己的 DM 安全策略：

| 策略 | 说明 |
|------|------|
| `pairing`（默认） | 陌生人需要配对验证才能使用 |
| `allowlist` | 只有白名单用户可以使用 |
| `open` | 任何人都可以使用 |

## Channel 列表

| Extension | 渠道 | 底层实现 |
|-----------|------|---------|
| `whatsapp` | WhatsApp | Baileys（WA Web 协议） |
| `telegram` | Telegram | grammY |
| `discord` | Discord | discord.js |
| `slack` | Slack | @slack/bolt |
| `feishu` | 飞书 | @larksuiteoapi/node-sdk |
| `googlechat` | Google Chat | Chat API |
| `signal` | Signal | signal-cli |
| `imessage` | iMessage（旧版） | imsg |
| `bluebubbles` | BlueBubbles（iMessage 推荐） | BlueBubbles Server API |
| `irc` | IRC | — |
| `msteams` | Microsoft Teams | — |
| `matrix` | Matrix | — |
| `line` | LINE | @line/bot-sdk |
| `mattermost` | Mattermost | — |
| `nextcloud-talk` | Nextcloud Talk | — |
| `nostr` | Nostr | — |
| `synology-chat` | Synology Chat | — |
| `tlon` | Tlon | — |
| `twitch` | Twitch | — |
| `zalo` | Zalo（商业版） | — |
| `zalouser` | Zalo（个人版） | — |

## 渠道管理命令

```bash
# 查看所有渠道连接状态
openclaw channels status --probe

# 登录 WhatsApp
openclaw channels login --channel whatsapp

# 登录第二个账号
openclaw channels login --channel whatsapp --account work

# 审批 DM 配对请求
openclaw pairing approve <channel> <code>
```

## 相关文档

- [[Gateway]] — Bindings 路由规则
- [[Session管理]] — Session key 与 dmScope
- [[DMWork集成]] — DMWork 如何作为 Channel 接入

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿 |
