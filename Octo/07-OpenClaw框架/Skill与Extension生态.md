---
title: Skill 与 Extension 生态
type: reference
project: openclaw
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, openclaw, skill, extension, clawhub]
---

# Skill 与 Extension 生态

[[概述|← 返回概述]]

OpenClaw 内置 **54 个 Skill** 和 **43 个 Extension**，构成丰富的生态体系。

> ⚠️ **修正**：内置技能实际为 54 个（非文档早期记录的 53 个）。经 `ls skills/ | wc -l` 源码验证确认。

## Skill 系统

### 什么是 Skill

Skill 是一个目录，包含 `SKILL.md` 文件（AgentSkills 规范），告诉 Agent 如何使用特定工具。

### Skill 加载优先级

```
1. <workspace>/skills/          ← 最高优先级（工作区专属，覆盖同名内置技能）
2. ~/.openclaw/skills/          ← 用户本地 Skills
3. npm 包内置 skills/           ← 最低优先级（内置 54 个）
4. skills.load.extraDirs 配置的额外目录（最低优先级）
```

### SKILL.md 格式

```markdown
---
name: nano-banana-pro
description: Generate or edit images via Gemini 3 Pro Image
metadata: {"openclaw":{"emoji":"🎨","requires":{"bins":["uv"],"env":["GEMINI_API_KEY"]},"primaryEnv":"GEMINI_API_KEY"}}
---

# 技能使用说明
...工具用法、示例、注意事项...
```

**Frontmatter 关键字段**：

| 字段 | 说明 |
|------|------|
| `name` | 技能标识符（唯一） |
| `description` | Agent 看到的技能描述，用于决定何时调用 |
| `metadata.openclaw.requires.bins` | 需要的系统命令（load-time 过滤） |
| `metadata.openclaw.requires.env` | 需要的环境变量 |
| `metadata.openclaw.requires.config` | 需要的配置项 |
| `metadata.openclaw.always: true` | 始终加载，跳过过滤 |
| `user-invocable: true/false` | 是否暴露为斜杠命令 |
| `command-dispatch: tool` | 斜杠命令直接派发到工具（绕过 LLM） |

### 内置 Skill 列表（54 个）

#### 🛠️ 生产力工具（9 个）

| Skill | 说明 |
|-------|------|
| `apple-reminders` | Apple 提醒事项管理（remindctl CLI） |
| `apple-notes` | Apple 笔记 |
| `bear-notes` | Bear 笔记 |
| `himalaya` | 邮件管理（IMAP/SMTP） |
| `notion` | Notion 文档 |
| `obsidian` | Obsidian 知识库（新版） |
| `obsidian-1-0-0` | Obsidian 知识库（旧版，与 obsidian 并存） |
| `things-mac` | Things 3 任务管理 |
| `trello` | Trello 看板 |

#### 💻 代码与开发（4 个）

| Skill | 说明 |
|-------|------|
| `coding-agent` | 委托 Codex/Claude Code/Pi 完成编码任务 |
| `github` | GitHub 操作（issues、PRs、CI） |
| `gh-issues` | GitHub 自动修 bug + 开 PR |
| `gemini` | Gemini CLI 集成 |

#### 🎬 媒体处理（7 个）

| Skill | 说明 |
|-------|------|
| `video-frames` | 用 ffmpeg 提取视频帧/片段 |
| `summarize` | 从 URL/播客/本地文件提取文本/字幕 |
| `openai-image-gen` | OpenAI 图像生成 |
| `nano-banana-pro` | Gemini 图像生成 |
| `openai-whisper` | 语音转文字（本地） |
| `openai-whisper-api` | 语音转文字（API） |
| `camsnap` | 摄像头拍照 |

#### 🎵 音乐与家居（4 个）

| Skill | 说明 |
|-------|------|
| `blucli` | BluOS 音频控制 |
| `spotify-player` | Spotify 播放控制 |
| `openhue` | Philips Hue 灯光控制 |
| `sonoscli` | Sonos 音箱控制 |

#### 🖥️ 系统与本地工具（11 个）

| Skill | 说明 |
|-------|------|
| `mcporter` | 调用 MCP 服务器/工具 |
| `peekaboo` | macOS UI 截图与自动化 |
| `session-logs` | 搜索历史 session 日志 |
| `weather` | 天气查询（wttr.in/Open-Meteo） |
| `healthcheck` | 安全审计与主机加固 |
| `node-connect` | 诊断 Node 连接问题 |
| `skill-creator` | 创建/改进 AgentSkill |
| `tmux` | tmux 会话管理 |
| `1password` | 1Password CLI 管理 |
| `gog` | Google Workspace CLI（Gmail/Calendar/Drive） |
| `ordercli` | Foodora 订单查询 CLI |

#### 💬 社交与通信（6 个）

| Skill | 说明 |
|-------|------|
| `discord` | Discord 操作（发消息、管理频道） |
| `slack` | Slack 操作 |
| `imsg` | iMessage 发送 |
| `wacli` | WhatsApp CLI |
| `bluebubbles` | BlueBubbles（iMessage）技能 |
| `dmwork`（用户）| DMWork 集成（本工作区扩展） |

#### 🎨 创意与其他（13 个）

| Skill | 说明 |
|-------|------|
| `nano-pdf` | PDF 处理 |
| `xurl` | URL 工具 |
| `goplaces` | 地点查询 |
| `model-usage` | 模型用量统计 |
| `clawhub` | ClawHub 技能市场操作 |
| `canvas` | Canvas 操作 |
| `oracle` | 运势/神谕类 |
| `blogwatcher` | 博客监控 |
| `sherpa-onnx-tts` | 本地 TTS（Sherpa ONNX） |
| `songsee` | 音频频谱图可视化 |
| `gifgrep` | GIF 搜索 |
| `eightctl` | — |
| `sag` | — |

### Skill 管理命令

```bash
# 列出所有技能
openclaw skills list

# 查看技能加载状态
openclaw skills status
```

---

## Extension 生态（43 个）

Extensions 位于 `/opt/homebrew/lib/node_modules/openclaw/extensions/`。

### Channel 类（21 个）

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

### Provider 类（7 个）

| Extension | Provider 标识 | 说明 |
|-----------|--------------|------|
| `ollama` | `ollama` | 本地 Ollama 服务 |
| `vllm` | `vllm` | 自托管 vLLM |
| `sglang` | `sglang` | 自托管 SGLang |
| `google-gemini-cli-auth` | `google-gemini-cli` | Google Gemini CLI OAuth |
| `minimax-portal-auth` | `minimax-portal` | MiniMax Portal OAuth |
| `qwen-portal-auth` | `qwen-portal` | 通义千问 Portal OAuth |
| `copilot-proxy` | `copilot-proxy` | GitHub Copilot 代理 |

### 运行时与功能类（15 个）

| Extension | 用途 |
|-----------|------|
| `acpx` | ACP 运行时后端（支持 Claude Code、Codex、Pi 等外部 harness） |
| `memory-core` | 核心记忆后端 |
| `memory-lancedb` | LanceDB 向量数据库记忆后端 |
| `llm-task` | LLM 任务调度 |
| `thread-ownership` | 线程归属管理（ACP/subagent 绑定到 IM 线程） |
| `diffs` | 代码差异展示 |
| `open-prose` | 散文/文本处理 |
| `lobster` | CLI 主题（lobster 调色板） |
| `device-pair` | 设备配对（iOS/Android Node 配对） |
| `phone-control` | Android 手机控制（通知/位置/SMS/照片/日历/运动数据） |
| `talk-voice` | 语音对话（Text-to-Speech + Speech-to-Text） |
| `voice-call` | 语音通话 |
| `diagnostics-otel` | OpenTelemetry 诊断 |
| `shared` | 公共代码（SDK 共享层） |
| `test-utils` | 内部测试工具（不在 Extension 文档列表中，但实际存在） |

## ClawHub 技能市场

OpenClaw 有公开的技能注册表 [clawhub.com](https://clawhub.com)，可以搜索、安装、更新、同步技能。

通过 `clawhub` skill 操作：
```
# 在 clawhub.com 上搜索技能
# 安装、更新、同步技能到本地
```

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿，修正技能数量为 54，补充缺失的 6 个技能（1password/gog/obsidian-1-0-0/ordercli/songsee/bluebubbles），补充 test-utils extension |
