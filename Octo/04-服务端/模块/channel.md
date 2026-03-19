---
title: channel 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, channel]
---

# channel 模块

## 功能职责

频道（Channel）管理模块，提供：
- 频道状态查询
- 消息自动删除设置（定时清除）
- 清空频道消息
- 群聊"故事线"（storyline）— 个人频道叙事流功能

> 此模块提供 `IService` 接口，供其他模块调用频道相关业务逻辑。

## API 端点表

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `/v1/channel/state` | 查询频道状态 | 用户 JWT |
| GET | `/v1/channels/:channel_id/:channel_type` | 获取频道信息 | 用户 JWT |
| POST | `/v1/channels/:channel_id/:channel_type/message/autodelete` | 设置消息定时删除 | 用户 JWT |
| POST | `/v1/channels/:channel_id/:channel_type/message/clear` | 清空频道消息 | 用户 JWT |
| GET | `/v1/channels/:channel_id/:channel_type/storyline` | 获取群聊个人故事线 | 用户 JWT |

## 关键数据模型

### channel_setting 表

| 字段 | 类型 | 说明 |
|------|------|------|
| `channel_id` | VARCHAR(80) | 频道 ID（最长 80 字符，支持 Space 前缀） |
| `channel_type` | smallint | 频道类型 |
| `parent_channel_id` | VARCHAR(40) | 父频道 ID |
| `parent_channel_type` | smallint | 父频道类型 |
| `msg_auto_delete` | integer | 消息定时删除时间（秒），0=不删除 |
| `offset_message_seq` | integer | 频道消息删除偏移 seq |

### 唯一索引

- `UNIQUE INDEX channel_setting_uidx (channel_id, channel_type)`

## 频道类型说明

| 值 | 类型 |
|----|------|
| 1 | 个人（单聊） |
| 2 | 群组 |
| 3 | 客服 |
| 4 | 社区 |
| 5 | 话题 |
| 6 | 资讯 |

## 相关模块

- [[message]] — 消息管理，依赖 channel 的偏移设置
- [[group]] — 群组对应 channel_type=2
- [[space]] — Space 内频道 ID 格式为 `s{space_id}_{channel_id}`

## 相关数据库表

- `channel_setting` — 频道配置表
- `channel_offset` / `channel_offset1` / `channel_offset2` — 用户频道偏移（分片表）

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
