---
title: Claude Code 适配器
type: reference
project: dmwork-adapters
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, adapter, claude-code, gateway, dmwork-adapters]
---

# Claude Code 适配器（claude-code-dmwork-ws）

## 产品定位

`claude-code-dmwork-ws` 是一个**独立的 Node.js 网关进程**，连接 Claude Agent SDK 与 DMWork WuKongIM，无框架约束，设计更简单直接。

## 架构

```
Claude Agent SDK
┌──────────────────┐
│  query() 函数    │
└──────────┬───────┘
           │
┌──────────▼────────────────────────────────┐
│          Gateway (gateway.ts)             │
│                                           │
│  SessionStore   GroupContext              │
│  (对话历史)     (群聊缓存)                │
│                                           │
│  dmwork/socket.ts                         │
│  (WuKongIM 协议 + DH/AES 加密)            │
│                                           │
│  dmwork/api.ts                            │
│  (DMWork REST API + Stream API)           │
└──────────────────────────────────────────┘
           │ WebSocket    │ HTTPS
     WuKongIM WS      DMWork API Server
```

## 核心组件

### gateway.ts — 核心网关

```typescript
class Gateway {
  sessions: SessionStore   // 对话历史持久化
  groupCtx: GroupContext   // 群聊缓存（成员映射 + 消息历史）
  socket: WKSocket         // WuKongIM 连接
  
  async start(): Promise<void>  // 注册机器人 → 建立 WS → 开始心跳
  stop(): void                  // 停止心跳 + 断开 WS
  
  private async onMessage(msg: BotMessage): Promise<void>
  // 处理流程：过滤 → 群聊@检测 → 历史构建 → Claude查询 → 发送回复
}
```

**消息过滤策略**：
- 丢弃旧消息（连接成功后前 3 秒的消息视为离线积压）
- 跳过自己发的消息
- 目前只处理文本消息

**锁文件机制**：防止多个 Gateway 实例同时运行（写 `.gateway.lock` 文件，检查 PID 是否存活）。

### session-store.ts — 会话存储

将对话历史以 JSON 文件持久化到磁盘（`dataDir/sessions/{key}.json`）：

```typescript
interface Session {
  peerId: string
  channelId: string
  channelType: number
  messages: MsgRecord[]  // 最多保留 MAX_HISTORY = 40 条
  updatedAt: number
}
```

`buildHistoryPrefix()` 将历史格式化为 prompt 前缀注入 Claude。

### group-context.ts — 群聊上下文缓存

内存缓存（Map），管理群聊的消息历史和成员映射：

```typescript
class GroupContext {
  pushMessage(channelId, fromUid, content, timestamp)  // 缓存所有群聊消息
  learnMember(channelId, uid, name)                    // 从消息元数据学习成员映射
  async refreshMembers(channelId, apiUrl, botToken)    // 从 API 获取完整成员列表（有 TTL）
  buildContext(channelId, limit)                       // 生成历史 prompt
  resolveMentions(channelId, text)                     // @name → uid[]（用于推送通知）
}
```

### dmwork/socket.ts

与 `openclaw-channel-dmwork` 共享几乎相同的 `WKSocket` 实现（两个适配器各有一份拷贝）。

详见 [[协议/WuKongIM二进制协议]]。

## 与 OpenClaw 适配器对比

| 维度 | openclaw-channel-dmwork | claude-code-dmwork-ws |
|------|------------------------|----------------------|
| 集成方式 | OpenClaw Channel Plugin | 独立 Node.js 进程 |
| AI 框架 | OpenClaw | Claude Agent SDK |
| 多账号 | ✅ 支持 | 单账号 |
| 配置热重载 | ✅ 支持 | 需重启 |
| 历史存储 | 内存（跨热重载持久） | JSON 文件持久化 |
| 框架依赖 | `openclaw` peer dep | `@anthropic-ai/claude-agent-sdk` |

## 相关页面

- [[概述]] — 适配层整体概述
- [[协议/WuKongIM二进制协议]] — socket.ts 底层协议
- [[消息处理/入站与出站]] — 消息处理管道

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
