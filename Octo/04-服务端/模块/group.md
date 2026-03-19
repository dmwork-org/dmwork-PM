---
title: group 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, group]
---

# group 模块

## 功能职责

群组核心业务模块，提供完整的群组生命周期管理：
- 群组创建、解散、信息修改
- 成员管理（添加/移除/同步/更新成员信息）
- 群管理员管理
- 全员禁言 / 单成员禁言
- 群二维码
- 群主转让
- 邀请入群（生成邀请链接 → H5 确认）
- 黑名单管理
- 群头像上传
- 扫码加入群
- 管理员后台：群列表、封禁/解禁群组

## API 端点表

### 用户端接口

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/group/create` | 创建群组 | 用户 JWT |
| GET | `/v1/group/my` | 我的群组列表 | 用户 JWT |
| GET | `/v1/group/forbidden_times` | 禁言时长列表 | 用户 JWT |
| POST | `/v1/groups/:group_no/members` | 添加成员 | 用户 JWT |
| DELETE | `/v1/groups/:group_no/members` | 移除成员 | 用户 JWT |
| POST | `/v1/groups/:group_no/members_delete` | 移除成员（兼容路径） | 用户 JWT |
| GET | `/v1/groups/:group_no/members` | 获取成员列表 | 用户 JWT |
| GET | `/v1/groups/:group_no/membersync` | 同步成员 | 用户 JWT |
| GET | `/v1/groups/:group_no` | 获取群信息 | 用户 JWT |
| PUT | `/v1/groups/:group_no/setting` | 修改群设置 | 用户 JWT |
| PUT | `/v1/groups/:group_no` | 修改群信息 | 用户 JWT |
| PUT | `/v1/groups/:group_no/members/:uid` | 修改成员信息 | 用户 JWT |
| POST | `/v1/groups/:group_no/exit` | 退出群聊 | 用户 JWT |
| POST | `/v1/groups/:group_no/managers` | 添加管理员 | 群主 |
| DELETE | `/v1/groups/:group_no/managers` | 移除管理员 | 群主 |
| POST | `/v1/groups/:group_no/forbidden/:on` | 全员禁言 | 群主/管理员 |
| GET | `/v1/groups/:group_no/qrcode` | 获取群二维码 | 用户 JWT |
| POST | `/v1/groups/:group_no/transfer/:to_uid` | 群主转让 | 群主 |
| POST | `/v1/groups/:group_no/member/invite` | 成员邀请 | 用户 JWT |
| GET | `/v1/groups/:group_no/member/h5confirm` | H5 确认邀请页面 | 无 |
| POST | `/v1/groups/:group_no/blacklist/:action` | 黑名单操作 | 群主/管理员 |
| POST | `/v1/groups/:group_no/forbidden_with_member` | 单成员禁言/解禁 | 群主/管理员 |
| POST | `/v1/groups/:group_no/avatar` | 上传群头像 | 群主/管理员 |
| DELETE | `/v1/groups/:group_no/disband` | 解散群组 | 群主 |
| GET | `/v1/groups/:group_no/detail` | 获取群详情 | 用户 JWT |
| GET | `/v1/groups/:group_no/avatar` | 获取群头像 | 无 |
| GET | `/v1/groups/:group_no/scanjoin` | 扫码加入群 | 用户 JWT |
| POST | `/v1/group/invite/sure` | 确认邀请 | 用户 JWT |
| GET | `/v1/group/invites/:invite_no` | 获取邀请详情 | 用户 JWT |

### 管理员接口

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `/v1/group/list` | 群列表 | 管理员 |
| GET | `/v1/group/disablelist` | 封禁群列表 | 管理员 |
| PUT | `/v1/group/liftban/:groupNo/:status` | 封禁/解禁群 | 管理员 |
| PUT | `/v1/groups/:group_no/forbidden/:on` | 群全员禁言 | 管理员 |
| GET | `/v1/groups/:group_no/members` | 查看群成员 | 管理员 |
| GET | `/v1/groups/:group_no/members/blacklist` | 群黑名单 | 管理员 |
| DELETE | `/v1/groups/:group_no/members` | 移除成员 | 管理员 |

## 关键数据模型

### group 表

| 字段 | 类型 | 说明 |
|------|------|------|
| `group_no` | VARCHAR(40) UNIQUE | 群唯一编号 |
| `name` | VARCHAR(40) | 群名字 |
| `creator` | VARCHAR(40) | 创建者 uid |
| `status` | smallint | 0=不正常 1=正常 |
| `forbidden` | smallint | 0=不禁言 1=全体禁言 |
| `group_type` | smallint | 0=普通群 1=超大群（>1000人） |
| `space_id` | VARCHAR(40) | 所属 Space ID（2026-03-07+） |
| `allow_member_pinned_message` | smallint | 允许成员置顶消息 |

### group_member 表

| 字段 | 类型 | 说明 |
|------|------|------|
| `group_no` | VARCHAR(40) | 群编号 |
| `uid` | VARCHAR(40) | 成员 uid |
| `role` | smallint | 0=普通 1=群主 2=管理员 |
| `robot` | smallint | 0=人类 1=机器人 |
| `forbidden_expir_time` | integer | 成员禁言时长（秒） |
| `invite_uid` | VARCHAR(40) | 邀请人 uid |

### group_setting 表

每个用户对每个群的个性化设置：

| 字段 | 说明 |
|------|------|
| `mute` | 是否免打扰 |
| `top` | 是否置顶 |
| `show_nick` | 是否显示昵称 |
| `flame` / `flame_second` | 阅后即焚设置 |
| `receipt` | 消息是否回执 |

## 事件发布

group 模块在关键操作后发布事件（通过 [[base|base 模块事件系统]]）：

| 操作 | 事件 |
|------|------|
| 创建群 | `group.create` |
| 更新群信息 | `group.update` |
| 添加成员 | `group.memberadd` |
| 移除成员 | `group.memberremove` |
| 解散群 | `group.disband` |
| 群成员邀请 | `group.member.invite` |
| 群主转让 | `group.member.transfer.grouper` |

## 相关数据库表

- `group` — 群组主表
- `group_member` — 群成员表
- `group_setting` — 用户群个性化设置
- `group_invite` — 群邀请记录（头部）
- `invite_item` — 群邀请明细

## 相关模块

- [[user]] — 用户信息（IService 被 group 使用）
- [[space]] — group.space_id 关联 Space
- [[message]] — 群聊消息路由（channel_type=2）
- [[search]] — 聚合搜索中的群组搜索

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
