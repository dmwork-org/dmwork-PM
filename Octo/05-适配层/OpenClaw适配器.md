---
title: OpenClaw 适配器
type: reference
project: dmwork-adapters
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, adapter, openclaw, channel-plugin, dmwork-adapters]
---

# OpenClaw 适配器（openclaw-channel-dmwork）

## 产品定位

`openclaw-channel-dmwork` 作为 OpenClaw 的 **Channel Plugin**，深度集成进框架生命周期。支持多账号、配置 Schema 声明式管理、状态上报等框架特性。

## 架构

```
OpenClaw Framework
┌──────────────────────────────────────────────────────┐
│  Channel Registry → Plugin SDK                       │
│                  (routing/session/reply/config)       │
└────────────────────┬─────────────────────────────────┘
                     │ ChannelPlugin interface
┌────────────────────▼─────────────────────────────────┐
│           openclaw-channel-dmwork                    │
│                                                      │
│  channel.ts   inbound.ts   mention-utils.ts          │
│  (入口/出站)  (消息处理)   (@mention 工具)            │
│       │            │                                  │
│  socket.ts   api-fetch.ts                            │
│  (WuKongIM 协议)  (REST API 调用)                    │
└──────────────────────────────────────────────────────┘
          │ WebSocket (binary)    │ HTTPS (REST)
    WuKongIM WS             DMWork API Server
```

## 核心文件功能

### channel.ts — 插件入口与出站处理

- 声明插件元数据（id, label, capabilities, configSchema）
- 实现 `outbound.sendText` — 文字回复出站
- 实现 `outbound.sendMedia` — 媒体文件出站（图片/文件）
- 实现 `gateway.startAccount` — 启动账号连接（WebSocket + 心跳 + 消息处理）
- 状态上报：`ctx.setStatus()` 向框架汇报账号状态

**消息目标格式**：
```typescript
// DM 单聊
ctx.to = "uid123"
// 群组
ctx.to = "group:channelId"
// 带@mention
ctx.to = "group:channelId@uid1,uid2"
```

### 多账号支持

通过 `accounts` 配置字段支持多个机器人账号并行运行，每个账号维护独立的 `WKSocket` 实例。

**模块级状态（跨热重载持久化）**：
```typescript
// 模块级 Map，跨热重载保持（#54）
const _historyMaps = new Map<string, Map<string, any[]>>();
const _memberMaps  = new Map<string, Map<string, string>>();
```

### api-fetch.ts — REST API 调用层

| 函数 | 端点 | 说明 |
|------|------|------|
| `registerBot()` | POST /v1/bot/register | 注册机器人 |
| `sendMessage()` | POST /v1/bot/sendMessage | 发送文本消息 |
| `uploadFile()` | POST /v1/bot/file/upload | 文件上传 |
| `sendTyping()` | POST /v1/bot/typing | 发送正在输入 |
| `sendReadReceipt()` | POST /v1/bot/readReceipt | 发送已读回执 |
| `sendHeartbeat()` | POST /v1/bot/heartbeat | 心跳保活 |
| `getGroupMembers()` | GET /v1/bot/groups/:id/members | 获取群成员 |
| `getChannelMessages()` | GET /v1/bot/channels/:id/messages | 获取频道历史 |

## 配置 Schema

```typescript
interface DmworkAccountConfig {
  name?: string
  enabled?: boolean
  botToken?: string           // 机器人 Token（必须）
  apiUrl?: string             // DMWork API 地址（默认 http://localhost:8090）
  wsUrl?: string              // WebSocket 地址（覆盖自动探测）
  cdnUrl?: string             // CDN 基础地址（media URL 拼接）
  heartbeatIntervalMs?: number // 心跳间隔（默认 30000ms）
  requireMention?: boolean    // 群聊是否需要@才响应（默认 true）
  historyLimit?: number       // 历史上下文条数（默认 20）
  historyPromptTemplate?: string // 历史注入模板
}
```

## 关键设计模式

### Bot-to-Bot 循环防护

```typescript
const _knownBotUids = new Set<string>();  // 模块级，记录所有已知 bot uid

// DM 场景过滤（群聊中不过滤，@mention 机制防循环）
if (_knownBotUids.has(msg.from_uid) && msg.channel_type === ChannelType.DM) return;
```

### 优雅降级策略

- 流式消息失败 → 降级为普通消息
- 缓存刷新失败 → 使用旧缓存继续（带短 backoff TTL）
- @mention 解析失败 → 跳过 mention，发纯文本
- Token 过期 → 重新注册获取新 Token（只重试一次，防循环）

### 缓存惰性清理

```typescript
const CACHE_MAX_AGE_MS = 4 * 60 * 60 * 1000;  // 4小时未活跃则清理
const CACHE_CLEANUP_INTERVAL_MS = 30 * 60 * 1000;
```

## 相关页面

- [[概述]] — 适配层整体概述
- [[协议/WuKongIM二进制协议]] — socket.ts 底层协议
- [[消息处理/入站与出站]] — inbound.ts 完整管道

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
