---
title: ACP 协作协议（Agent Collaboration Protocol）
type: reference
project: openclaw
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, openclaw, acp, codex, claude-code, subagent]
---

# ACP 协作协议（Agent Collaboration Protocol）

[[概述|← 返回概述]]

ACP（Agent Client Protocol）让 OpenClaw 可以运行外部编码 Agent（harness），如 Claude Code、Codex、Pi、OpenCode、Gemini CLI，并将其绑定到 IM 线程。

## 核心概念

| 概念 | 说明 |
|------|------|
| ACP Session | 一个独立的 session，key 格式：`agent:<agentId>:acp:<uuid>` |
| ACP Backend | 由 `acpx` extension 实现 |
| 线程绑定 | ACP Session 可绑定到 Discord Thread/Telegram Topic，后续消息自动路由 |
| Harness | 外部 AI 编码工具（Claude Code、Codex、Pi、OpenCode 等） |

## ACP vs 子 Agent（Subagent）

| 维度 | ACP Session | Sub-agent |
|------|------------|-----------|
| Runtime | 外部 harness（Claude Code/Codex 等） | OpenClaw 原生 |
| Session Key | `acp:<uuid>` | `subagent:<uuid>` |
| 操作命令 | `/acp ...` | `/subagents ...` |
| 创建工具 | `sessions_spawn (runtime:"acp")` | `sessions_spawn` |
| 适用场景 | 外部 AI 工具集成 | 内部任务分解 |

## 支持的 Harness

| Harness | 启动方式 | 特殊要求 |
|---------|---------|---------|
| Claude Code | `--print --permission-mode bypassPermissions` | 无 PTY |
| Codex | `pty: true` | 需要 PTY |
| Pi | `pty: true` | 需要 PTY |
| OpenCode | `pty: true` | 需要 PTY |
| Gemini CLI | — | 见 gemini skill |

## 操作命令

```bash
# 启动 ACP Session 并绑定到当前线程
/acp spawn codex --mode persistent --thread auto

# 查看 ACP Session 状态
/acp status

# 切换模型
/acp model zai/glm-5

# 停止当前 turn
/acp cancel

# 关闭 ACP Session
/acp close
```

CLI 等效：
```bash
# 启动 ACP harness session
openclaw acp spawn <target>

# 查看 ACP session 状态
openclaw acp status
```

## 线程绑定机制

当 ACP Session 绑定到 IM 线程时：

```
用户 → 发消息到 Discord Thread
  → Gateway 检测到 thread_id 有 ACP binding
  → 路由到 ACP Session
  → ACP Session 转发给外部 Harness
  → Harness 返回结果
  → 发送回 Discord Thread
```

线程绑定由 `thread-ownership` extension 管理。

## acpx Extension

`acpx` 是 ACP 的核心后端 extension，负责：
- 管理 ACP Session 生命周期
- 与外部 Harness 进程通信（stdin/stdout/PTY）
- 将 Harness 输出格式化为 OpenClaw 消息
- 处理 ACP 命令（`/acp spawn`、`/acp cancel` 等）

## 相关文档

- [[Session管理]] — Session key 格式
- [[Plugin系统]] — acpx extension 加载

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿 |
