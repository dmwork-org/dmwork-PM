---
title: "ADR-002: Space Channel ID 前缀约定"
type: decision
project: octo
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: accepted
tags: [octo, adr, space, multi-tenancy, decision]
---

# ADR-002: Space Channel ID 前缀约定

> 用 `s{spaceId}_` 前缀约定代替独立表结构，以零数据迁移成本实现多租户隔离。

## 概述

为了支持 Space 多租户场景，选择在 Channel ID 中嵌入 Space 标识（前缀约定），而不是创建独立的 Space 消息表。

---

## 状态

**已接受（Accepted）**

---

## 背景与问题

Octo 需要支持企业级多租户（Space），不同 Space 的用户和群组需要相互隔离：
- 用户 A 在 Space 1 的会话，不应该与用户 A 在 Space 2 的会话混淆
- 群聊 G1 在 Space 1 和 Space 2 中应该是完全独立的频道
- Bot 的 API Key 需要与具体 Space 绑定

**核心问题**：如何在 WuKongIM 的 Channel 系统中表达"这个 Channel 属于哪个 Space"？

---

## 决策

**采用 Channel ID 前缀约定**：

```
s{spaceId}_{userId}     ← Space 内的 DM 频道
s{spaceId}_{groupId}    ← Space 内的群聊频道
```

例如：
- Space `abc123` 中用户 `user001` 的 DM Channel：`sabc123_user001`
- Space `abc123` 中群聊 `group001`：`sabc123_group001`

**数据模型**（仅 3 张表）：

```sql
-- Space 主表
CREATE TABLE space (
  id BIGINT PRIMARY KEY,
  space_no VARCHAR(40) NOT NULL,   -- Space 唯一编号
  name VARCHAR(100),
  creator_uid VARCHAR(40),
  max_member_count INT DEFAULT 500,
  created_at DATETIME
);

-- Space 成员表
CREATE TABLE space_member (
  space_no VARCHAR(40) NOT NULL,
  uid VARCHAR(40) NOT NULL,
  role TINYINT DEFAULT 0,          -- 0=member, 1=admin, 2=owner
  created_at DATETIME,
  PRIMARY KEY (space_no, uid)
);

-- Space 预设频道表（新成员自动加入）
CREATE TABLE space_preset_channel (
  space_no VARCHAR(40) NOT NULL,
  channel_id VARCHAR(100) NOT NULL,   -- 含 s{spaceId}_ 前缀
  channel_type SMALLINT NOT NULL,
  PRIMARY KEY (space_no, channel_id, channel_type)
);
```

**API 路径（已验证）**：`/v1/space/`（注意：无 's'，不是 `/v1/spaces/`）

---

## 理由

### 优势

1. **零数据迁移**：前缀约定完全在应用层实现，不需要修改 WuKongIM 的消息存储结构，现有消息表（`message_0~4`）无需改动。

2. **极简数据模型**：只需 3 张表（space、space_member、space_preset_channel），不需要为 Space 创建独立的消息表、会话表等。

3. **透明兼容**：WuKongIM 无感知——它只是在存储和路由 Channel ID 字符串，不关心前缀的业务含义。

4. **查询简单**：判断一条消息是否属于某 Space，只需检查 `channel_id.startsWith("s{spaceId}_")`。

5. **快速实现**：几乎不需要修改现有代码，只需在创建 Space 频道时加前缀，查询时过滤前缀。

### 劣势与权衡

1. **Channel ID 可读性降低**：`sabc123_user001` 不如独立字段直观。
   - 权衡：接受。对最终用户不可见，只在 API 层面，开发者能读懂。

2. **前缀污染**：所有 Space 内的 Channel ID 都携带前缀，查询时需要注意不带前缀的全局频道（无前缀）和带前缀的 Space 频道区别。
   - 权衡：接受。业务逻辑层统一处理，API 调用者不需要感知。

3. **Space 间数据无法完全物理隔离**：消息仍在同一张 `message_N` 表中，只是 `channel_id` 不同。
   - 权衡：接受。对于企业内部使用的私有化部署，逻辑隔离已足够。如果需要强物理隔离，需要另外的方案（如独立数据库）。

4. **Space ID 变更困难**：一旦 Space 创建，其所有 Channel ID 含有该 spaceId，无法轻易更改 spaceId。
   - 权衡：接受。Space ID 是系统生成的，不应该由用户决定，也不应该变更。

---

## 后果

**正向影响**：
- Space 功能快速上线，无需大规模数据库重构
- 不增加 WuKongIM 侧的改动，与底层通讯层完全解耦

**需要注意**：
- 所有创建 Space 内 Channel 的代码，必须加 `s{spaceId}_` 前缀
- 查询"某 Space 内的所有频道"时，使用 `channel_id LIKE 's{spaceId}_%'`
- Space ID 前缀约定是隐式契约，必须在文档中明确说明

---

## API 路径勘误

> ⚠️ **重要**：文档中曾出现 `/v1/spaces/` 的错误路径。

根据源码核验（`verification_dmworkim.md`），实际路径为：

```go
// modules/space/api.go
auth := r.Group("/v1/space", ...)   // 单数，无 's'
open := r.Group("/v1/space")
```

**正确路径**：
- `POST /v1/space/create`
- `GET  /v1/space/my`
- `POST /v1/space/join`
- `GET  /v1/space/:space_id`

---

## 相关页面

- [[Space多租户]] — Space 功能完整说明
- [[ADR-001-双层架构]] — 前置架构决策
- [[ADR-003-Bot-Token体系]] — Bot 与 Space 的 API Key 绑定

---

## CHANGELOG

| 版本 | 日期 | 变更说明 |
|------|------|----------|
| 0.1.0 | 2026-03-19 | 初始版本，含路径勘误（spaces → space） |
