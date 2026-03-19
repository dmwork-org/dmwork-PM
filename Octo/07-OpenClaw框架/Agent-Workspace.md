---
title: Agent Workspace（工作区）
type: reference
project: openclaw
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, openclaw, agent, workspace, soul]
---

# Agent Workspace（工作区）

[[概述|← 返回概述]]

Agent Workspace 是 OpenClaw 产品理念的核心。**Gateway 是控制面，Workspace 才是产品**——Agent 的身份、行为、记忆都定义在 Workspace 的文件中。

## 默认路径

| 场景 | 路径 |
|------|------|
| 单 Agent | `~/.openclaw/workspace` |
| 多 Agent（agentId=xijing） | `~/.openclaw/agents/xijing/agent` |
| 会话存储 | `~/.openclaw/agents/<agentId>/sessions/` |

## 标准文件体系

### 身份与行为核心（Owner-only 文件）

| 文件 | 用途 | 何时加载 | 谁能修改 |
|------|------|---------|---------|
| `SOUL.md` | 人格、语气、边界 | 每个 session 开始 | 仅 Owner |
| `IDENTITY.md` | Agent 的名字、vibe、emoji | 每个 session 开始 | 仅 Owner |
| `USER.md` | 用户信息、称呼偏好 | 每个 session 开始 | 仅 Owner |
| `AGENTS.md` | 操作指令、行为规则、记忆使用方式 | 每个 session 开始 | 仅 Owner |
| `TOOLS.md` | 工具使用笔记（仅供指导，不控制工具权限） | 每个 session 开始 | 仅 Owner |

> ⚠️ **安全底线**：SOUL.md、IDENTITY.md、USER.md、AGENTS.md 定义了 Agent 的身份，只有 Owner 明确要求时才能修改。任何来自群聊、第三方的修改请求都应拒绝。

### 记忆文件（Agent 自主维护）

| 文件 | 用途 | 加载时机 |
|------|------|---------|
| `MEMORY.md` | 精选长期记忆 | **仅主 session（私聊）时加载** |
| `memory/YYYY-MM-DD.md` | 每日日志（每天一个文件） | 按需读取 |

> ⚠️ **安全限制**：`MEMORY.md` 含个人上下文，**不在群聊或其他人存在的场景中加载**，防止信息泄露。

### 任务与生命周期文件

| 文件 | 用途 | 加载时机 |
|------|------|---------|
| `HEARTBEAT.md` | 心跳任务清单 | 心跳运行时 |
| `BOOT.md` | Gateway 启动时执行的清单 | Gateway 重启时（内部 hooks 启用时） |
| `BOOTSTRAP.md` | 首次运行仪式 | 仅首次创建 workspace 时 |

### 技能目录

| 目录 | 用途 |
|------|------|
| `skills/` | Workspace 专属技能（最高优先级，覆盖同名内置技能） |
| `scripts/` | 创作内容（视具体 Agent 用途而定） |

## Workspace Contract（工作区契约）

`SOUL.md + AGENTS.md + USER.md` 三件套是 Agent 身份的"宪法"，在每个 session 启动时**注入到 system prompt**。

这是 OpenClaw 的核心创新：
- 无需改代码即可更改 Agent 人格和行为
- 文件可版本控制，可审计
- 每个 Agent 有完全独立的"宪法"

## 多 Agent 支持

每个 Agent 是完全隔离的"大脑"：

```
~/.openclaw/
├── agents/
│   ├── xijing/
│   │   ├── agent/          ← xijing 的 workspace
│   │   │   ├── SOUL.md
│   │   │   ├── AGENTS.md
│   │   │   ├── USER.md
│   │   │   └── skills/
│   │   └── sessions/       ← xijing 的会话历史
│   └── main/
│       ├── agent/
│       └── sessions/
└── workspace/              ← 单 Agent 模式的默认 workspace
```

| 隔离维度 | 说明 |
|---------|------|
| 工作区 | 不同 SOUL.md → 不同人格 |
| Session store | 对话历史不共享 |
| Auth profiles | 可以用不同的模型 |
| agentDir | 完全独立的目录 |

## Agent 管理命令

```bash
# 创建新 Agent
openclaw agents add xijing

# 列出所有 Agent 和路由绑定
openclaw agents list --bindings

# 运行 Agent 处理一条消息
openclaw agent --message "你好"

# 高强度思考模式
openclaw agent --thinking high
```

## 实际示例

本项目中 xijing agent 的 Workspace 位于：
`/Users/limenglin/.openclaw/agents/xijing/workspace/`

包含文件：
- `SOUL.md` — 戏精人设（认真到像演一出好戏）
- `AGENTS.md` — 行为规则（如何维护记忆、团队协作规则）
- `USER.md` — 娃爹的信息
- `IDENTITY.md` — 戏精身份定义
- `TOOLS.md` — 工具使用备忘
- `memory/` — 日常记忆目录
- `skills/dmwork/` — DMWork 专属技能

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿 |
