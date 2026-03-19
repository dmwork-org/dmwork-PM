---
title: Gateway 控制面
type: reference
project: openclaw
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, openclaw, gateway, websocket]
---

# Gateway 控制面

[[概述|← 返回概述]]

Gateway 是 OpenClaw 的长驻进程，是整个助手框架的控制面（Control Plane）。

## 核心职责

| 职责 | 说明 |
|------|------|
| HTTP + WebSocket 服务 | 单端口（默认 18789）同时承载多种服务 |
| Channel Plugin 管理 | 启动/监控所有已配置的渠道连接 |
| Session 路由 | 将消息路由到对应 Agent |
| 配置热重载 | 支持热安全变更不重启 |
| 设备配对 | 所有 WS 客户端需要设备配对认证 |
| 服务化 | 支持 launchd（macOS）和 systemd（Linux）用户服务 |

## 单端口多服务（Single Port Multi-Service）

默认端口 `18789`，同时承载：

```
ws://127.0.0.1:18789
  ├── WebSocket 控制/RPC API
  │     └── CLI、macOS App、WebChat 都通过这里连接
  ├── HTTP API
  │     ├── OpenAI 兼容接口
  │     ├── Responses API
  │     └── Tools invoke HTTP API
  ├── Control UI（浏览器管理界面）
  └── Canvas Host（Agent 驱动的可视化工作区）
```

## WebSocket 协议

### 连接握手

第一帧必须是 `connect`，包含设备身份 + 可选 auth token：

```json
{
  "type": "connect",
  "deviceId": "cli-abc123",
  "authToken": "optional-token"
}
```

### 消息格式

| 方向 | 格式 |
|------|------|
| 请求 | `{"type":"req", "id":"...", "method":"...", "params":{...}}` |
| 响应 | `{"type":"res", "id":"...", "ok":true, "payload":{...}}` |
| 错误响应 | `{"type":"res", "id":"...", "ok":false, "error":{...}}` |
| 事件推送 | `{"type":"event", "event":"...", "payload":{...}, "seq":42}` |

### 幂等性

请求支持 `idempotencyKey` 字段，防止重试副作用。

## 配置热重载

通过 `gateway.reload.mode` 控制热重载行为：

| 模式 | 行为 |
|------|------|
| `off` | 禁用热重载，需手动重启 |
| `hot` | 所有变更直接热应用（可能不安全） |
| `restart` | 所有变更触发重启 |
| `hybrid`（**默认**） | 热安全变更直接应用，需要重启的变更自动重启 |

热安全变更示例：调整某渠道配置、更新 Provider 设置
需要重启的变更示例：修改监听端口、变更核心加密配置

## 设备配对（Pairing）

所有 WebSocket 客户端（CLI、macOS App、iOS/Android Node）都需要设备配对：

```
本地连接（loopback）→ 可自动授权
非本地连接 → 需要显式审批
```

审批命令：
```bash
openclaw pairing approve <channel> <code>
```

## 服务化安装

### macOS（launchd）
```bash
openclaw gateway install
# 安装为 launchd 用户服务，开机自动启动
```

### Linux（systemd）
```bash
openclaw gateway install
# 安装为 systemd 用户服务
```

## Gateway 管理命令

| 命令 | 说明 |
|------|------|
| `openclaw gateway` | 前台启动 |
| `openclaw gateway --port 18789 --verbose` | 指定端口+详细日志 |
| `openclaw gateway status` | 查看运行状态 |
| `openclaw gateway start` | 启动服务 |
| `openclaw gateway stop` | 停止服务 |
| `openclaw gateway restart` | 重启 |
| `openclaw gateway install` | 安装为系统服务 |
| `openclaw status` | 综合状态概览 |
| `openclaw health` | 健康检查 |
| `openclaw logs --follow` | 实时查看日志 |

## Bindings 路由规则

Bindings 是 Gateway 的消息路由规则，**most-specific wins**：

```
peer 精确匹配 > parentPeer > guildId+roles > guildId > teamId > accountId > 渠道级 > 默认
```

配置示例：
```json5
{
  bindings: [
    // 特定 DM 发给 xijing agent
    {
      agentId: "xijing",
      match: { channel: "dmwork", peer: { kind: "direct", id: "merlin" } }
    },
    // 整个 dmwork 渠道默认发给 main agent
    { agentId: "main", match: { channel: "dmwork" } }
  ]
}
```

## 相关文档

- [[Channel系统]] — Channel Plugin 如何在 Gateway 内工作
- [[Session管理]] — Session key 格式与路由
- [[概述]] — 整体架构

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿 |
