---
title: message 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, message, conversation, reaction]
---

# message 模块

## 功能职责

消息系统的核心业务模块，覆盖消息的全生命周期：
- 消息同步（写模式 / 频道同步）
- 消息删除（单向 / 双向）、撤回
- 语音消息已读标记
- 消息搜索
- typing 状态推送
- 消息扩展（Extra）同步
- 已读回执
- 敏感词 / 违禁词管理
- 消息编辑
- 提醒（Reminder）系统（@我 / 草稿）
- 置顶消息
- 消息表情回应（Reaction）
- 最近会话（Conversation）管理
- 代发消息（管理员功能）

## API 端点表

### 消息操作

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/message/sync` | 同步消息（写模式） | 用户 JWT |
| POST | `/v1/message/syncack/:last_message_seq` | 同步回执 | 用户 JWT |
| DELETE | `/v1/message` | 删除消息（单向） | 用户 JWT |
| DELETE | `/v1/message/mutual` | 双向删除消息 | 用户 JWT |
| POST | `/v1/message/revoke` | 撤回消息 | 用户 JWT |
| POST | `/v1/message/offset` | 清除频道消息 | 用户 JWT |
| PUT | `/v1/message/voicereaded` | 语音已读 | 用户 JWT |
| POST | `/v1/message/search` | 消息搜索 | 用户 JWT |
| POST | `/v1/message/typing` | typing 状态 | 用户 JWT |
| POST | `/v1/message/channel/sync` | 同步频道消息 | 用户 JWT |
| POST | `/v1/message/extra/sync` | 同步消息扩展 | 用户 JWT |
| POST | `/v1/message/readed` | 消息已读 | 用户 JWT |
| GET | `/v1/message/sync/sensitivewords` | 同步敏感词 | 用户 JWT |
| POST | `/v1/message/edit` | 消息编辑 | 用户 JWT |
| POST | `/v1/msg/send` | 代发消息 | 用户 JWT |

### 提醒（Reminder）

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/message/reminder/sync` | 同步提醒 | 用户 JWT |
| POST | `/v1/message/reminder/done` | 提醒完成 | 用户 JWT |
| GET | `/v1/message/prohibit_words/sync` | 同步违禁词 | 用户 JWT |

### 置顶消息

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/message/pinned` | 置顶消息 | 用户 JWT |
| POST | `/v1/message/pinned/sync` | 同步置顶 | 用户 JWT |
| POST | `/v1/message/pinned/clear` | 清除置顶 | 用户 JWT |
| GET | `/v1/messages/:message_id/receipt` | 消息回执列表 | 用户 JWT |

### 消息回应（Reaction）

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/reactions` | 添加/取消消息回应 | 用户 JWT |
| POST | `/v1/reaction/sync` | 同步消息回应 | 用户 JWT |

### 最近会话（Conversation）

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `/v1/conversations` | 获取最近会话列表 | 用户 JWT |
| PUT | `/v1/conversation/clearUnread` | 清除未读数 | 用户 JWT |
| POST | `/v1/conversation/sync` | 同步最近会话 | 用户 JWT |
| POST | `/v1/conversation/syncack` | 同步最近会话回执 | 用户 JWT |
| POST | `/v1/conversation/extra/sync` | 同步会话扩展 | 用户 JWT |
| DELETE | `/v1/conversations/:channel_id/:channel_type` | 删除最近会话 | 用户 JWT |
| POST | `/v1/conversations/:channel_id/:channel_type/extra` | 更新会话扩展 | 用户 JWT |

### 管理员接口

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/message/send` | 发送消息 | 管理员 |
| POST | `/v1/message/sendfriends` | 给用户好友批量发消息 | 管理员 |
| GET | `/v1/message` | 代发消息记录 | 管理员 |
| POST | `/v1/message/sendall` | 给所有用户发消息 | 管理员 |
| GET | `/v1/message/record` | 消息记录 | 管理员 |
| GET | `/v1/message/recordpersonal` | 单聊聊天记录 | 管理员 |
| POST | `/v1/message/prohibit_words` | 添加违禁词 | 管理员 |
| GET | `/v1/message/prohibit_words` | 查询违禁词 | 管理员 |
| DELETE | `/v1/message/prohibit_words` | 删除违禁词 | 管理员 |
| DELETE | `/v1/message` | 删除消息 | 管理员 |

## 关键数据模型

### message_extra 表（消息扩展）

| 字段 | 说明 |
|------|------|
| `message_id` | 消息全局唯一 ID |
| `revoke` | 是否撤回 |
| `revoker` | 撤回者 uid |
| `content_edit` | 编辑后的正文 |
| `edited_at` | 编辑时间戳 |
| `readed_count` | 已读数量 |
| `is_pinned` | 是否置顶 |

### reminders 表（提醒）

| 字段 | 说明 |
|------|------|
| `reminder_type` | 1=@我 2=草稿 |
| `uid` | 提醒的用户 uid |
| `message_seq` | 触发提醒的消息序号 |
| `publisher` | 提醒发布者 uid |

### conversation_extra 表（会话扩展）

| 字段 | 说明 |
|------|------|
| `browse_to` | 浏览到的最大 message_seq（影响未读数） |
| `keep_message_seq` | 会话保持位置 |
| `draft` | 草稿内容 |

### reaction_users 表（消息回应）

| 字段 | 说明 |
|------|------|
| `message_id` | 消息 ID |
| `uid` | 回应用户 uid |
| `emoji` | 回应 emoji |
| `is_deleted` | 是否已取消 |

## 消息存储架构

消息采用双层存储：
1. **WuKongIM 侧** — 消息投递和实时推送
2. **DMWorkim 侧** — 通过 Webhook 接收并持久化（`message` / `message1~4` 分片表）

消息偏移系统：

| 表 | 粒度 | 说明 |
|----|------|------|
| `channel_offset` | 用户 × 频道 | 用户总体读取位置 |
| `device_offset` | 用户 × 设备 × 频道 | 多设备读取位置 |
| `user_last_offset` | 用户 × 频道 | 最新消息位置 |
| `conversation_extra.browse_to` | 用户 × 频道 | 未读消息数计算 |

## 相关数据库表

- `message` / `message1~4` — 消息主体（5 分片表）
- `message_extra` — 消息扩展（撤回/编辑/已读/置顶）
- `member_readed` — 已读用户列表
- `message_user_extra` / `message_user_extra1~2` — 用户消息状态（3 分片）
- `conversation_extra` — 会话进度/草稿
- `reaction_users` — Emoji 回应
- `reminders` / `reminder_done` — @提醒系统
- `pinned_message` — 置顶消息
- `prohibit_words` — 违禁词表
- `send_history` — 管理员发送记录

## 相关模块

- [[webhook]] — 接收 WuKongIM 消息并写入 message 表
- [[search]] — 消息全局搜索
- [[channel]] — 频道消息清除/偏移

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
