---
title: Bot 数据模型
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, dmworkim, database, bot, robot]
---

# Bot 数据模型

[[概览|← 返回概览]]

Bot（机器人）在 DMWorkim 中是一个**特殊用户**，核心设计是 `robot.robot_id = user.uid`。

## 核心设计：Bot = 特殊 User

```
user 表：
  uid = "robot_abc123"
  robot = 1              ← 标志字段，区分机器人
  category = ""
  
robot 表：
  robot_id = "robot_abc123"  ← 与 user.uid 相同
  bot_token = "bf_xxx"       ← Bot API 认证凭证
  creator_uid = "user_xxx"   ← 谁创建的这个 Bot
```

这种设计的好处：
- Bot 可以直接参与消息系统（发消息、加群等）
- Bot 的好友关系、群组关系都沿用用户体系
- 权限系统统一处理

## robot 表

| 字段 | 类型 | 说明 |
|------|------|------|
| id | bigint PK | 自增主键 |
| robot_id | VARCHAR(40) | 机器人 ID（= user.uid）UNIQUE |
| token | VARCHAR(100) | 内部 Token（旧字段） |
| version | BIGINT | 同步版本号 |
| status | smallint | 状态：0=禁用，1=启用 |
| inline_on | smallint | 是否开启行内搜索（Inline Query）|
| placeholder | VARCHAR(40) | 行内搜索输入框占位符 |
| username | VARCHAR(40) | 机器人 username |
| app_id | VARCHAR(40) FK | 所属 App ID |
| creator_uid | VARCHAR(40) FK | 创建者 UID（2026-02-26 新增） |
| description | VARCHAR(500) | 机器人描述（2026-02-26 新增） |
| bot_token | VARCHAR(100) | Bot API 认证 Token（bf_ 前缀）（2026-02-26 新增）UNIQUE |
| im_token_cache | VARCHAR(200) | 缓存的 IM Token（2026-02-26 新增） |
| bot_commands | VARCHAR(1000) | 命令列表 JSON（2026-02-26 新增） |
| auto_approve | tinyint | 是否自动通过好友申请（2026-03-07 新增） |
| created_at | timestamp | 创建时间 |
| updated_at | timestamp | 更新时间 |

**索引：**
- `UNIQUE INDEX robot_id_robot_index (robot_id)`
- `UNIQUE INDEX idx_robot_bot_token (bot_token)` ← 用于 Bot API 认证
- `INDEX idx_robot_creator_uid (creator_uid)`

## robot_menu 表

定义机器人支持的命令/菜单项。

| 字段 | 类型 | 说明 |
|------|------|------|
| id | bigint PK | 自增主键 |
| robot_id | VARCHAR(40) FK | 关联 robot.robot_id |
| cmd | VARCHAR(100) | 命令（如 `/help`） |
| remark | VARCHAR(100) | 命令说明 |
| type | VARCHAR(100) | 命令类型 |
| created_at | timestamp | 创建时间 |
| updated_at | timestamp | 更新时间 |

**索引：**
- `INDEX bot_id_robot_menu_index (robot_id)`

## app 表

App 是 Bot 认证的"第二层身份"，用于 Robot 模块的 `authRobot` 鉴权：

| 字段 | 类型 | 说明 |
|------|------|------|
| app_id | VARCHAR(40) PK | App 唯一标识 |
| app_key | VARCHAR(40) | App 密钥（用于 HMAC 验签） |
| app_name | VARCHAR(40) | App 名称 |
| app_logo | VARCHAR(400) | App Logo URL |
| status | integer | 状态：0=禁用，1=可用 |

`authRobot` 鉴权流程：
```
路径参数 :robot_id + :app_key
  → 查询 robot 表（robot_id）
  → 查询 app 表（app_id = robot.app_id）
  → HMAC(app_key, secret) 比较
  → 验证通过
```

## user_api_key 表（BotFather）

用于 BotFather OpenAI 兼容接口的 API Key 认证：

| 字段 | 类型 | 说明 |
|------|------|------|
| id | bigint PK | 自增主键 |
| uid | varchar(40) FK | 用户 UID |
| api_key | varchar(100) | API Key（uk_ 前缀）UNIQUE |
| space_id | varchar(40) FK | 绑定的 Space ID（2026-03-18 新增） |

**索引：**
- `UNIQUE KEY uk_uid_space (uid, space_id)` ← 每个用户每个 Space 只能有一个 API Key

## apply / bot_apply 表（user 模块）

Bot 好友申请流程相关表（属于 user 模块，位于 `/v1/robot/apply*` 路由下）：

### apply 表

| 字段 | 类型 | 说明 |
|------|------|------|
| id | bigint PK | 自增主键 |
| uid | VARCHAR(40) FK | 申请人 UID |
| to_uid | VARCHAR(40) FK | 被申请人 UID |
| status | smallint | 状态：0=待处理，1=已确认，2=已拒绝 |
| token | VARCHAR(40) | 申请 Token |
| created_at | timestamp | 创建时间 |
| updated_at | timestamp | 更新时间 |

## Bot 生命周期

```
1. 用户在平台创建 Bot
   → user 表插入（robot=1）
   → robot 表插入（bot_token=bf_xxx）

2. Bot 程序调用 POST /v1/bot/register（bot_token 认证）
   → 获取 im_token（WuKongIM 连接用）
   → 获取 owner_channel_id（Bot 的频道 ID）
   → robot.im_token_cache 更新

3. Bot 程序建立 WuKongIM WebSocket 连接（im_token 认证）
   → 接收消息事件

4. Bot 通过 POST /v1/bot/sendMessage 发送消息

5. 管理员通过 /v1/manager/robots/* 管理 Bot
```

## Space 内的 Bot 频道

当 Bot 在 Space 内使用时，channel_id 格式为：

```
s{space_id}_{robot_id}

示例：s_space123_robot_abc
```

`robot/event.go` 中通过 `strings.LastIndex("_")` 提取真实 robot_id：
```go
if strings.HasPrefix(uid, "s") {
    if idx := strings.LastIndex(uid, "_"); idx >= 0 {
        realUID = uid[idx+1:]
    }
}
```

---

## 相关文档

- [[概览]] — 数据库整体
- [[表关系图]] — ER 图
- [[Bot-API]] — Bot API 参考

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿，包含 2026-02-26/03-07/03-18 新增字段 |
