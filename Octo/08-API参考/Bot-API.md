---
title: Bot API 参考
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, dmworkim, api, bot, botfather]
---

# Bot API 参考

DMWorkim BotFather 模块提供的 Bot API，供 AI Agent/Bot 使用。

> **认证**：大多数端点需要 `Authorization: Bearer {bot_token}` Header。
> `bot_token` 以 `bf_` 前缀开头，通过 `/v1/bot/register` 获得。

## 端点总览

| 分类 | 端点数量 |
|------|---------|
| 注册与心跳 | 2 |
| 消息发送 | 4 |
| 消息同步 | 1 |
| 流式消息 | 2 |
| 事件系统 | 2 |
| 群组管理 | 3 |
| 命令系统 | 1 |
| 文件管理 | 4 |
| 用户 Bot 管理 | 5 |
| Bot 申请流程 | 4 |

---

## 注册与心跳

### POST /v1/bot/register

Bot 自注册。**无需认证**，只需 bot_token（通过用户界面创建 Bot 后获得）。

**Request Body：**
```json
{
  "bot_token": "bf_xxxxxxxxxxxxxxxx"
}
```

**Response：**
```json
{
  "robot_id": "robot_abc123",
  "im_token": "im_token_xxx",
  "ws_url": "wss://im.example.com",
  "api_url": "https://im.example.com",
  "owner_uid": "user_uid_xxx",
  "owner_channel_id": "channel_id_xxx"
}
```

> ⚠️ **修正**：`owner_channel_id` 字段在早期文档中遗漏，经 `src/types.ts` 验证确认实际返回。

### POST /v1/bot/heartbeat

Bot 心跳，保持在线状态。

**Request Body：** 空

**Response：** 200 OK

---

## 消息发送

### POST /v1/bot/sendMessage

Bot 发送消息到指定频道。

**Request Body：**
```json
{
  "channel_id": "group_no 或 user_uid",
  "channel_type": 2,
  "stream_no": "",
  "payload": {
    "type": 1,
    "content": "Hello, World!"
  }
}
```

**channel_type 值：**

| 值 | 含义 |
|----|------|
| 1 | 个人频道 |
| 2 | 群组频道 |
| 3 | 客服频道 |
| 5 | 社区频道 |
| 6 | 话题频道 |

### POST /v1/bot/typing

Bot 发送输入状态（"正在输入..."）。

**Request Body：**
```json
{
  "channel_id": "...",
  "channel_type": 2
}
```

### POST /v1/bot/readReceipt

Bot 发送阅读回执。

**Request Body：**
```json
{
  "message_id": "...",
  "channel_id": "...",
  "channel_type": 2
}
```

### POST /v1/bot/setCommands

设置 Bot 命令列表。

**Request Body：**
```json
{
  "commands": [
    { "cmd": "/help", "remark": "获取帮助", "type": "text" },
    { "cmd": "/start", "remark": "开始", "type": "text" }
  ]
}
```

---

## 消息同步

### POST /v1/bot/messages/sync

> ⚠️ **修正**：此端点是 **POST**（非 GET），经 `api-fetch.ts` 源码验证确认。

同步频道历史消息。

**Request Body：**
```json
{
  "channel_id": "...",
  "channel_type": 2,
  "start_message_seq": 0,
  "end_message_seq": 0,
  "limit": 20,
  "pull_mode": 0
}
```

**pull_mode 值：**

| 值 | 说明 |
|----|------|
| 0 | 向下拉取（从旧到新） |
| 1 | 向上拉取（从新到旧） |

**Response：**
```json
{
  "messages": [
    {
      "message_id": "...",
      "message_seq": 42,
      "client_msg_no": "...",
      "from_uid": "...",
      "channel_id": "...",
      "channel_type": 2,
      "timestamp": 1710000000,
      "payload": { "type": 1, "content": "Hello" }
    }
  ]
}
```

---

## 流式消息

用于 AI 逐字输出场景。

### POST /v1/bot/stream/start

开始流式消息，返回 `stream_no`。

**Request Body：**
```json
{
  "channel_id": "...",
  "channel_type": 2,
  "payload": {}
}
```

**Response：**
```json
{
  "stream_no": "stream_abc123"
}
```

### POST /v1/bot/stream/end

结束流式消息。

**Request Body：**
```json
{
  "stream_no": "stream_abc123",
  "channel_id": "...",
  "channel_type": 2
}
```

**流式消息完整流程：**
```
POST /v1/bot/stream/start  → 获得 stream_no
POST /v1/bot/sendMessage   → { stream_no, payload: { content: "Hello" } }
POST /v1/bot/sendMessage   → { stream_no, payload: { content: "Hello World" } }
... （多次追加）
POST /v1/bot/stream/end    → 结束
```

---

## 事件系统

### POST /v1/bot/events

获取 Bot 事件（长轮询）。

**Request Body：**
```json
{
  "event_id": 0,
  "limit": 10
}
```

**Response：**
```json
{
  "events": [
    {
      "event_id": 1,
      "event_type": "message",
      "message": { ... },
      "expire": 1710000060
    }
  ]
}
```

### POST /v1/bot/events/:event_id/ack

确认事件已处理。

**Path Params：** `event_id` — 事件 ID

---

## 群组管理

### GET /v1/bot/groups

获取 Bot 所在的群组列表。

### GET /v1/bot/groups/:group_no

获取指定群组信息。

### GET /v1/bot/groups/:group_no/members

获取群组成员列表。

**Response 示例：**
```json
{
  "members": [
    {
      "uid": "user_xxx",
      "name": "张三",
      "role": 1
    }
  ]
}
```

---

## 文件管理

### POST /v1/bot/file/upload

上传文件（主路径）。

**Request：** multipart/form-data

**Response：**
```json
{
  "url": "/v1/botfile/xxx/file.jpg",
  "path": "xxx/file.jpg"
}
```

### POST /v1/bot/upload

上传文件（兼容旧路径，等同 `/v1/bot/file/upload`）。

另有媒体上传路径：`POST /v1/bot/upload?type=chat`（inbound 媒体转发时使用）。

### GET /v1/bot/file/download/*path

Bot 文件下载。

### GET /v1/botfile/*path

Bot 文件代理访问（public URL）。

### POST /v1/botfile/upload

Bot 文件代理上传。

---

## Bot 技能文档

### GET /v1/bot/skill.md

获取 Bot 技能描述文档（Markdown）。**无需认证**。

---

## 用户 Bot 管理

> 这些端点供用户在平台内自行创建和管理 Bot。

### POST /v1/user/bots

创建用户 Bot。

### GET /v1/user/bots

列出当前用户的 Bot。

### PUT /v1/user/bots/:bot_id

更新指定 Bot。

### DELETE /v1/user/bots/:bot_id

删除指定 Bot。

### GET /v1/user/bots/:bot_id/token

获取 Bot Token（用于程序调用）。

---

## Bot 申请流程

> ⚠️ **修正**：以下端点前缀是 `/v1/robot/`（非 `/v1/bot/`），经 `api_apply.go` 源码验证确认。

### POST /v1/robot/apply

Bot 申请加好友（Bot 向用户发好友申请）。

### POST /v1/robot/apply/sure

确认好友申请。

### PUT /v1/robot/apply/refuse/:apply_id

拒绝好友申请。

### GET /v1/robot/applies

获取好友申请列表。

---

## 错误码

| HTTP 状态码 | 含义 |
|------------|------|
| 200 | 成功 |
| 400 | 请求参数错误 |
| 401 | 认证失败（bot_token 无效） |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

---

## 相关文档

- [[用户API]] — 用户端 API 参考
- [[管理API]] — 管理员 API 参考
- [[DMWork集成]] — OpenClaw 接入指南

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿，修正 /v1/bot/messages/sync 为 POST，修正 register 响应包含 owner_channel_id，修正 apply 系列路径为 /v1/robot/ |
