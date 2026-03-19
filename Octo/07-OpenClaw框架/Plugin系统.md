---
title: Plugin 系统
type: reference
project: openclaw
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, openclaw, plugin, extension, lifecycle]
---

# Plugin 系统

[[概述|← 返回概述]]

Plugin 系统是 OpenClaw 可扩展性的核心机制，通过标准化接口实现渠道、Provider、功能的插件化。

## Plugin 声明（openclaw.plugin.json）

每个插件在根目录下放置 `openclaw.plugin.json`：

```json
{
  "id": "discord",
  "channels": ["discord"],
  "configSchema": {
    "type": "object",
    "properties": {
      "token": { "type": "string" }
    }
  },
  "skills": ["./skills"]
}
```

## Plugin 类型

通过 `openclaw.plugin.json` 中的字段区分类型：

| 类型 | 声明字段 | 示例 |
|------|---------|------|
| Channel 插件 | `channels: [...]` | discord, telegram, whatsapp |
| Provider 插件 | `providers: [...]` | ollama, vllm, sglang |
| 记忆后端插件 | `kind: "memory"` | memory-core, memory-lancedb |
| 功能扩展插件 | 无特殊字段 | acpx, diffs, thread-ownership |

## 加载机制

插件从以下位置加载：

```
优先级（高 → 低）：
  1. ~/.openclaw/plugins/（用户自定义插件）
  2. /opt/homebrew/lib/node_modules/openclaw/extensions/（内置）
```

启用/禁用插件：
```bash
# 查看所有插件
openclaw plugins list

# 启用插件
openclaw plugins enable google-gemini-cli-auth

# 禁用插件
openclaw plugins disable vllm
```

或通过配置：
```json5
{
  plugins: {
    entries: {
      "google-gemini-cli-auth": { enabled: true }
    }
  }
}
```

## Hook 生命周期

Plugin 可以在以下钩子点拦截/增强 Gateway 行为：

### 模型与 Prompt 钩子

| 钩子 | 触发时机 | 典型用途 |
|------|---------|---------|
| `before_model_resolve` | session 预加载阶段 | 覆盖 provider/model |
| `before_prompt_build` | system prompt 构建前 | 注入额外 context |
| `before_agent_start` | Agent 开始前 | 兼容性 hook |
| `agent_end` | Agent 运行结束 | 审计、日志 |

### 上下文压缩钩子

| 钩子 | 触发时机 |
|------|---------|
| `before_compaction` | 上下文即将压缩 |
| `after_compaction` | 上下文压缩完成 |

### 工具钩子

| 钩子 | 触发时机 | 典型用途 |
|------|---------|---------|
| `before_tool_call` | 工具调用前 | 拦截、修改参数 |
| `after_tool_call` | 工具调用后 | 审计、后处理 |
| `tool_result_persist` | 写入 transcript 前 | 转换工具结果 |

### 消息钩子

| 钩子 | 触发时机 |
|------|---------|
| `message_received` | 收到 inbound 消息 |
| `message_sending` | 即将发送 outbound 消息 |
| `message_sent` | 消息已发送 |

### 会话与 Gateway 钩子

| 钩子 | 触发时机 |
|------|---------|
| `session_start` | Session 开始 |
| `session_end` | Session 结束 |
| `gateway_start` | Gateway 启动 |
| `gateway_stop` | Gateway 停止 |

## 内置 Extensions（43 个）

详见 [[Skill与Extension生态]] — Extension 分类清单。

### 分类概览

| 分类 | 数量 | 说明 |
|------|------|------|
| Channel 类 | 21 | IM 渠道集成 |
| Provider 类 | 7 | 模型提供商 |
| 运行时与功能类 | 15 | ACP、记忆、语音、诊断等 |

## 相关文档

- [[Channel系统]] — Channel Plugin 接口详解
- [[Provider体系]] — Provider Plugin 配置
- [[Skill与Extension生态]] — 完整 Extension 列表

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿 |
