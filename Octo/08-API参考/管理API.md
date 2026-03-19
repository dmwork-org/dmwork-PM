---
title: 管理 API 参考
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, dmworkim, api, manager, admin]
---

# 管理 API 参考

DMWorkim 管理员后台 API，前缀为 `/v1/manager/`。

> **认证**：需要管理员 JWT Token。通过 `/v1/manager/login` 获取。

---

## 管理员认证

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/manager/login` | 管理员登录 |

---

## 用户管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/manager/user/list` | 用户列表（分页） |
| POST | `/v1/manager/user/add` | 添加用户 |
| POST | `/v1/manager/user/resetpassword` | 重置用户密码 |
| POST | `/v1/manager/user/updatepassword` | 修改用户密码 |
| GET | `/v1/manager/user/friends` | 查看某用户的好友列表 |
| GET | `/v1/manager/user/blacklist` | 黑名单用户列表 |
| GET | `/v1/manager/user/disablelist` | 封禁用户列表 |
| GET | `/v1/manager/user/online` | 在线设备信息 |
| PUT | `/v1/manager/user/liftban/:uid/:status` | 封禁/解禁用户 |
| GET | `/v1/manager/user/devices` | 查看用户设备列表 |

### 管理员账号管理

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/manager/user/admin` | 添加管理员 |
| GET | `/v1/manager/user/admin` | 查询管理员列表 |
| DELETE | `/v1/manager/user/admin` | 删除管理员 |

---

## 群组管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/group/list` | 群组列表（分页） |
| GET | `/v1/group/disablelist` | 封禁群组列表 |
| PUT | `/v1/group/liftban/:groupNo/:status` | 封禁/解禁群组 |
| PUT | `/v1/groups/:group_no/forbidden/:on` | 群全员禁言 |
| GET | `/v1/groups/:group_no/members` | 查看群成员 |
| GET | `/v1/groups/:group_no/members/blacklist` | 群黑名单 |
| DELETE | `/v1/groups/:group_no/members` | 移除群成员 |

---

## Robot（机器人）管理

> ⚠️ **修正**：以下 8 个端点在早期文档中完全遗漏，经 `robot/api_manager.go` 源码验证确认。

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/manager/robots` | 机器人列表（分页） |
| GET | `/v1/manager/robots/:robot_id` | 机器人详情 |
| PUT | `/v1/manager/robots/:robot_id` | 编辑机器人信息 |
| DELETE | `/v1/manager/robots/:robot_id` | 删除机器人 |
| POST | `/v1/manager/robots/:robot_id/revoke_token` | 重置机器人 Token |
| GET | `/v1/manager/robot/menus` | 机器人菜单列表 |
| DELETE | `/v1/manager/robot/:robot_id/:id` | 删除机器人菜单 |
| PUT | `/v1/manager/robot/status/:robot_id/:status` | 修改机器人状态（启用/禁用） |

---

## Common（通用配置）管理

> ⚠️ **修正**：以下 6 个端点在早期文档中完全遗漏，经 `common/api_manager.go` 源码验证确认。

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/manager/common/appconfig` | 获取 App 全局配置 |
| POST | `/v1/manager/common/appconfig` | 修改 App 全局配置 |
| GET | `/v1/manager/common/appmodule` | 获取 App 模块列表 |
| PUT | `/v1/manager/common/appmodule` | 修改 App 模块 |
| POST | `/v1/manager/common/appmodule` | 新增 App 模块 |
| DELETE | `/v1/manager/common/:sid/appmodule` | 删除 App 模块 |

App 模块（AppModule）是控制前端功能开关的核心机制，例如：
- `base`：基础模块
- `login`：登录模块
- `rtc`：音视频模块

---

## 消息管理

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/message/send` | 代发消息（管理员） |
| POST | `/v1/message/sendfriends` | 给用户好友批量发消息 |
| POST | `/v1/message/sendall` | 给所有用户发消息 |
| GET | `/v1/message` | 代发消息记录 |
| GET | `/v1/message/record` | 消息记录 |
| GET | `/v1/message/recordpersonal` | 单聊聊天记录 |
| DELETE | `/v1/message` | 删除消息 |

### 违禁词管理

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/message/prohibit_words` | 添加违禁词 |
| GET | `/v1/message/prohibit_words` | 违禁词列表 |
| DELETE | `/v1/message/prohibit_words` | 删除违禁词 |

---

## 版本管理

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/common/appversion` | 添加 App 版本 |

---

## 统计

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/statistics/countnum` | 总数量统计（用户数、群数等） |
| GET | `/v1/statistics/registeruser/:start_date/:end_date` | 区间注册用户统计 |
| GET | `/v1/statistics/createdgroup/:start_date/:end_date` | 区间建群统计 |

---

## 举报管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/report/list` | 举报列表 |
| POST | `/v1/report/session/resolve` | 解决举报会话 |

---

## Workplace（工作台）管理

| 方法 | 路径 | 说明 |
|------|------|------|
| 见工作台模块 | `/v1/workplace/*` | 工作台应用管理 |

---

## 相关文档

- [[Bot-API]] — Bot API
- [[用户API]] — 用户端 API

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿，补充 Robot 管理员 API（8个）和 Common 管理员 API（6个） |
