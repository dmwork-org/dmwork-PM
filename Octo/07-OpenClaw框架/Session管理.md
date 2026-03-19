---
title: Session 管理
type: reference
project: openclaw
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, openclaw, session, routing]
---

# Session 管理

[[概述|← 返回概述]]

Session 是 OpenClaw 保持对话连续性的核心概念。每个 Session 代表一段独立的对话上下文。

## Session Key 格式

Session Key 是结构化字符串，编码了消息的完整路由信息：

| 场景 | 格式 | 示例 |
|------|------|------|
| 直接对话（DM） | `agent:<agentId>:<mainKey>` | `agent:xijing:main` |
| 群聊 | `agent:<agentId>:<channel>:group:<id>` | `agent:xijing:dmwork:group:123` |
| 频道 | `agent:<agentId>:<channel>:channel:<id>` | `agent:xijing:discord:channel:456` |
| Cron 任务 | `cron:<job.id>` | `cron:daily-summary` |
| Webhook | `hook:<uuid>` | `hook:abc-123` |
| 子 Agent | `agent:<agentId>:subagent:<uuid>` | `agent:xijing:subagent:e79280fb` |
| ACP Session | `agent:<agentId>:acp:<uuid>` | `agent:xijing:acp:f1234567` |

## DM 隔离模式（dmScope）

`session.dmScope` 控制私聊如何路由到 Session：

| 模式 | 行为 | 推荐场景 |
|------|------|---------|
| `main`（**默认**） | 所有 DM 共享同一个 main session | 单用户 |
| `per-peer` | 按发送者隔离 | — |
| `per-channel-peer` | 按渠道+发送者隔离 | 多用户 |
| `per-account-channel-peer` | 按账号+渠道+发送者隔离 | 多账号 |

配置示例：
```json5
{
  session: {
    dmScope: "per-channel-peer"
  }
}
```

## Session 重置策略

| 策略 | 配置/触发方式 | 说明 |
|------|-------------|------|
| 每日重置 | `session.dailyResetHour`（默认凌晨 4 点） | 定时自动重置 |
| 空闲超时 | `session.idleMinutes` | 超过指定分钟无消息则重置 |
| 手动重置 | 用户发送 `/new` 或 `/reset` | 立即开始新 session |

## Session 持久化

```
~/.openclaw/agents/<agentId>/
├── sessions/
│   ├── sessions.json          ← Session 索引（key → session 元数据）
│   └── <SessionId>.jsonl      ← 每个 session 的 transcript（JSONL 格式）
```

## 上下文压缩（Compaction）

当对话历史超过 token 限制时，自动触发压缩：

1. `before_compaction` hook 触发（插件可拦截）
2. 对历史消息进行 LLM 摘要压缩
3. `after_compaction` hook 触发
4. 压缩后的摘要替换原始历史

## Session 管理命令

```bash
# 列出所有 session
openclaw sessions

# 预览 session 清理计划
openclaw sessions cleanup --dry-run

# 执行 session 清理
openclaw sessions cleanup --enforce
```

## 相关文档

- [[Gateway]] — Bindings 路由规则
- [[Channel系统]] — Inbound 消息路由
- [[ACP协作协议]] — ACP Session 特殊场景

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿 |
