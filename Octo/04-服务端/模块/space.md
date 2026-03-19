---
title: space 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, space, multi-tenant]
---

# space 模块

## 功能职责

Space（空间）是平台的**多租户隔离单元**，类似 Discord 的服务器（Server）概念。每个 Space 拥有独立的：
- 成员体系（角色：Owner / Admin / Member）
- 频道命名空间（channel_id 添加 `s{spaceId}_` 前缀隔离）
- 预设群组（新成员加入时自动加入指定群组）
- 邀请链接系统

## API 端点表

> ⚠️ **CORRECTED**: 所有路由路径为 `/v1/space/`（无 's'），不是 `/v1/spaces/`

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/space/create` | 创建空间 | 用户 JWT |
| GET | `/v1/space/my` | 我的空间列表 | 用户 JWT |
| POST | `/v1/space/join` | 加入空间 | 用户 JWT |
| GET | `/v1/space/:space_id` | 获取空间信息 | 用户 JWT |
| PUT | `/v1/space/:space_id` | 更新空间信息 | Owner/Admin |
| DELETE | `/v1/space/:space_id` | 解散空间 | Owner |
| GET | `/v1/space/:space_id/members` | 空间成员列表 | 用户 JWT |
| POST | `/v1/space/:space_id/members/add` | 添加成员 | Owner/Admin |
| POST | `/v1/space/:space_id/members/remove` | 移除成员 | Owner/Admin |
| POST | `/v1/space/:space_id/leave` | 退出空间 | 用户 JWT |
| PUT | `/v1/space/:space_id/members/:uid/role` | 更新成员角色 | Owner |
| POST | `/v1/space/:space_id/invite` | 创建邀请 | Owner/Admin |
| PUT | `/v1/space/:space_id/invite/:code` | 更新邀请 | Owner/Admin |
| GET | `/v1/space/invite/:invite_code` | 获取邀请信息 | 无 |
| GET | `/v1/space/invite/:invite_code/preview` | 获取邀请预览 | 无 |

## 关键数据模型

### space 表

| 字段 | 类型 | 说明 |
|------|------|------|
| `space_id` | VARCHAR(40) UNIQUE | 空间唯一 ID |
| `name` | VARCHAR(100) | 空间名称 |
| `creator` | VARCHAR(40) | 创建者 uid |
| `status` | smallint | 1=正常 0=已解散 |
| `max_users` | INT | 最大成员数（0=不限，2026-03-10+） |
| `preset_group_ids` | TEXT | 预设群组 ID 列表（JSON 数组，2026-03-10+） |

### space_member 表

| 字段 | 说明 |
|------|------|
| `space_id` | 空间 ID |
| `uid` | 成员 uid（也可以是 robot_id） |
| `role` | 0=普通成员 1=管理员 2=拥有者（Owner） |
| `status` | 1=正常 0=已移除 |

### space_invitation 表

| 字段 | 说明 |
|------|------|
| `invite_code` | 邀请码（唯一） |
| `max_uses` | 最大使用次数（0=不限） |
| `used_count` | 已使用次数 |
| `expires_at` | 过期时间（NULL=永不过期） |
| `status` | 1=有效 0=无效 |

## 多租户隔离设计

### Channel ID 编码规范

```
普通频道：{peer_id}
Space 内频道：s{space_id}_{peer_id}
```

`pkg/space/channel.go` 实现：

```go
const SpaceChannelPrefix = "s"

// 构建 Space 内频道 ID
BuildChannelID(spaceID, peerID string) string
// ""   → peerID          （普通频道）
// "abc" → "sabc_peerID"  （Space 频道）

// 解析频道 ID，提取 spaceID 和 peerID
ParseChannelID(channelID string) (spaceID, peerID string)
// 优先最长前缀匹配（O(n) 线性查找已知 spaceId 列表）
// 回退 LastIndex 解析
```

### 预设群组机制

创建 Space 时可指定 `preset_group_ids`，新成员通过邀请链接加入时自动被拉入这些群组，实现"加入 Space 即加入工作群"。

### Space 数据演进

| 日期 | 变更 |
|------|------|
| 2026-03-07 | 创建 space、space_member、space_invitation 表 |
| 2026-03-07 | group 表增加 space_id 字段 |
| 2026-03-08 | 历史 Bot 自动加入 Space（数据迁移） |
| 2026-03-10 | space 表增加 max_users、preset_group_ids |

## 相关数据库表

- `space` — 空间主表
- `space_member` — 空间成员
- `space_invitation` — 邀请码

## 相关模块

- [[group]] — group.space_id 关联 Space；预设群组
- [[user]] — 用户加入 Space 后注册
- [[robot]] — Bot 也是 Space 成员（space_member.uid = robot.robot_id）
- [[botfather]] — user_api_key 绑定 space_id

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建，修正路由路径（/v1/space/ 无 's'） |
