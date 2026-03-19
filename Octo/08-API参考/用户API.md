---
title: 用户 API 参考
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, dmworkim, api, user, friend, group, message]
---

# 用户 API 参考

DMWorkim 面向普通用户的 API，按功能分组。

> **认证**：大多数端点需要 JWT Token，通过登录接口获取后放入 `Authorization: Bearer {token}` Header。
> 标注"（无需鉴权）"的端点可公开访问。

---

## 用户账号

### 注册 / 登录

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/user/register` | 手机号注册 |
| POST | `/v1/user/login` | 手机号+短信登录 |
| POST | `/v1/user/usernameregister` | 用户名注册 |
| POST | `/v1/user/usernamelogin` | 用户名登录 |
| POST | `/v1/user/emailregister` | 邮箱注册 |
| POST | `/v1/user/emaillogin` | 邮箱登录 |
| POST | `/v1/user/email/sendcode` | 发送邮箱验证码 |
| GET | `/v1/user/web3verifytext` | 获取 Web3 验签文本 |
| POST | `/v1/user/web3verifysign` | Web3 签名验证登录 |
| POST | `/v1/user/grant_login` | 授权登录（PC 扫码） |
| POST | `/v1/user/quit` | 退出登录 |
| POST | `/v1/user/pc/quit` | 退出 PC 登录 |

### 用户信息

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/users/:uid` | 获取用户信息 |
| GET | `/v1/users/:uid/avatar` | 用户头像（无需鉴权） |
| POST | `/v1/users/:uid/avatar` | 上传头像 |
| PUT | `/v1/user/current` | 修改当前用户信息 |
| PUT | `/v1/user/my/setting` | 更新个人设置 |
| PUT | `/v1/users/:uid/setting` | 更新用户设置 |
| GET | `/v1/user/search` | 搜索用户 |
| GET | `/v1/user/qrcode` | 获取我的二维码 |
| POST | `/v1/user/web3publickey` | 上传 Web3 公钥 |

### 密码管理

| 方法 | 路径 | 说明 |
|------|------|------|
| PUT | `/v1/user/updatepassword` | 修改登录密码 |
| POST | `/v1/user/sms/forgetpwd` | 短信找回密码 |
| POST | `/v1/user/email/forgetpwd` | 邮箱找回密码 |
| POST | `/v1/user/pwdforget_web3` | Web3 重置密码 |
| POST | `/v1/user/chatpwd` | 设置聊天密码 |
| POST | `/v1/user/lockscreenpwd` | 设置锁屏密码 |
| DELETE | `/v1/user/lockscreenpwd` | 关闭锁屏密码 |
| PUT | `/v1/user/lock_after_minute` | 设置锁屏延迟时间 |

### 账号注销

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/user/sms/destroy` | 发送注销验证码 |
| DELETE | `/v1/user/destroy/:code` | 注销账号 |

---

## 设备管理

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/user/device_token` | 注册推送设备（Token） |
| DELETE | `/v1/user/device_token` | 注销推送设备 |
| POST | `/v1/user/device_badge` | 更新设备徽章数（角标） |
| GET | `/v1/user/devices` | 设备列表 |
| DELETE | `/v1/user/devices/:device_id` | 删除设备 |
| GET | `/v1/user/online` | 在线设备列表 |
| POST | `/v1/user/online` | 批量查询在线状态 |

---

## 好友系统

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/user/friend/apply` | 发送好友申请 |
| GET | `/v1/user/friend/apply` | 好友申请列表 |
| DELETE | `/v1/user/friend/apply/:to_uid` | 删除好友申请 |
| PUT | `/v1/user/friend/refuse/:to_uid` | 拒绝好友申请 |
| POST | `/v1/user/friend/sure` | 确认好友申请 |
| GET | `/v1/user/friend/sync` | 同步好友列表 |
| GET | `/v1/user/friend/search` | 搜索好友 |
| PUT | `/v1/user/friend/remark` | 设置好友备注 |
| DELETE | `/v1/user/friends/:uid` | 删除好友 |

---

## 黑名单

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/user/blacklist/:uid` | 添加黑名单 |
| DELETE | `/v1/user/blacklist/:uid` | 移除黑名单 |
| GET | `/v1/user/blacklists` | 黑名单列表 |

---

## 通讯录 & 红点

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/user/maillist` | 添加通讯录 |
| GET | `/v1/user/maillist` | 获取通讯录 |
| GET | `/v1/user/reddot/:category` | 获取红点数 |
| DELETE | `/v1/user/reddot/:category` | 清除红点 |
| GET | `/v1/user/customerservices` | 客服列表（无需鉴权） |

---

## 群组（Group）

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/group/create` | 创建群组 |
| GET | `/v1/group/my` | 我的群组列表 |
| GET | `/v1/groups/:group_no` | 获取群信息 |
| GET | `/v1/groups/:group_no/detail` | 获取群详情 |
| PUT | `/v1/groups/:group_no` | 修改群信息 |
| PUT | `/v1/groups/:group_no/setting` | 修改群设置 |
| POST | `/v1/groups/:group_no/avatar` | 上传群头像 |
| GET | `/v1/groups/:group_no/avatar` | 获取群头像（无需鉴权） |
| POST | `/v1/groups/:group_no/members` | 添加群成员 |
| DELETE | `/v1/groups/:group_no/members` | 移除群成员 |
| GET | `/v1/groups/:group_no/members` | 群成员列表 |
| GET | `/v1/groups/:group_no/membersync` | 同步群成员 |
| PUT | `/v1/groups/:group_no/members/:uid` | 修改成员信息 |
| POST | `/v1/groups/:group_no/exit` | 退出群聊 |
| DELETE | `/v1/groups/:group_no/disband` | 解散群组 |
| POST | `/v1/groups/:group_no/managers` | 添加管理员 |
| DELETE | `/v1/groups/:group_no/managers` | 移除管理员 |
| POST | `/v1/groups/:group_no/forbidden/:on` | 全员禁言 |
| POST | `/v1/groups/:group_no/forbidden_with_member` | 单成员禁言/解禁 |
| GET | `/v1/group/forbidden_times` | 禁言时长列表 |
| POST | `/v1/groups/:group_no/blacklist/:action` | 群黑名单操作 |
| GET | `/v1/groups/:group_no/qrcode` | 群二维码 |
| GET | `/v1/groups/:group_no/scanjoin` | 扫码加入群 |
| POST | `/v1/groups/:group_no/transfer/:to_uid` | 群主转让 |
| POST | `/v1/groups/:group_no/member/invite` | 邀请成员 |
| GET | `/v1/groups/:group_no/member/h5confirm` | H5 邀请确认页（无需鉴权） |
| POST | `/v1/group/invite/sure` | 确认邀请 |
| GET | `/v1/group/invites/:invite_no` | 获取邀请详情 |

---

## 消息（Message）

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/message/sync` | 同步消息（写模式） |
| POST | `/v1/message/syncack/:last_message_seq` | 同步回执 |
| POST | `/v1/message/channel/sync` | 同步频道消息 |
| POST | `/v1/message/extra/sync` | 同步消息扩展 |
| DELETE | `/v1/message` | 删除消息（单向） |
| DELETE | `/v1/message/mutual` | 双向删除消息 |
| POST | `/v1/message/revoke` | 撤回消息 |
| POST | `/v1/message/offset` | 清除频道消息 |
| PUT | `/v1/message/voicereaded` | 语音消息已读 |
| POST | `/v1/message/readed` | 消息已读 |
| POST | `/v1/message/search` | 搜索消息 |
| POST | `/v1/message/typing` | 发送输入状态 |
| POST | `/v1/message/edit` | 编辑消息 |
| GET | `/v1/messages/:message_id/receipt` | 消息已读回执列表 |

### 提醒 & 置顶

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/message/reminder/sync` | 同步提醒（@我等） |
| POST | `/v1/message/reminder/done` | 提醒完成 |
| POST | `/v1/message/pinned` | 置顶消息 |
| POST | `/v1/message/pinned/sync` | 同步置顶 |
| POST | `/v1/message/pinned/clear` | 清除置顶 |

### Reaction（消息回应）

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/reactions` | 添加/取消消息 emoji 回应 |
| POST | `/v1/reaction/sync` | 同步消息回应 |

### 敏感词 & 违禁词

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/message/sync/sensitivewords` | 同步敏感词 |
| GET | `/v1/message/prohibit_words/sync` | 同步违禁词 |

---

## 会话（Conversation）

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/conversations` | 获取最近会话列表 |
| PUT | `/v1/conversation/clearUnread` | 清除未读数 |
| POST | `/v1/conversation/sync` | 同步最近会话 |
| POST | `/v1/conversation/syncack` | 同步会话回执 |
| POST | `/v1/conversation/extra/sync` | 同步会话扩展 |
| DELETE | `/v1/conversations/:channel_id/:channel_type` | 删除会话 |
| POST | `/v1/conversations/:channel_id/:channel_type/extra` | 更新会话扩展 |

---

## 频道（Channel）

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/channel/state` | 查询频道状态 |
| GET | `/v1/channels/:channel_id/:channel_type` | 获取频道信息 |
| POST | `/v1/channels/:channel_id/:channel_type/message/autodelete` | 设置消息定时删除 |
| POST | `/v1/channels/:channel_id/:channel_type/message/clear` | 清空频道消息 |
| GET | `/v1/channels/:channel_id/:channel_type/storyline` | 获取个人故事线 |

---

## Space（团队空间）

> ⚠️ **修正**：路径为 `/v1/space/`（无 's'），非文档早期记录的 `/v1/spaces/`，经 `space/api.go` 验证确认。

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/space/create` | 创建 Space |
| GET | `/v1/space/my` | 我的 Space 列表 |
| POST | `/v1/space/join` | 加入 Space |
| GET | `/v1/space/:space_id` | 获取 Space 信息 |
| PUT | `/v1/space/:space_id` | 更新 Space 信息 |
| DELETE | `/v1/space/:space_id` | 解散 Space |
| GET | `/v1/space/:space_id/members` | Space 成员列表 |
| POST | `/v1/space/:space_id/members/add` | 添加成员 |
| POST | `/v1/space/:space_id/members/remove` | 移除成员 |
| POST | `/v1/space/:space_id/leave` | 退出 Space |
| PUT | `/v1/space/:space_id/members/:uid/role` | 更新成员角色 |
| POST | `/v1/space/:space_id/invite` | 创建邀请链接 |
| PUT | `/v1/space/:space_id/invite/:code` | 更新邀请设置 |
| GET | `/v1/space/invite/:invite_code` | 邀请信息（无需鉴权） |
| GET | `/v1/space/invite/:invite_code/preview` | 邀请预览（无需鉴权） |

---

## 搜索 & 工具

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/search/global` | 全局搜索（用户/群组/消息） |
| GET | `{QRCodeInfoURL}` | 二维码解析（路径可配置） |
| GET | `/v1/openapi/access_token` | 获取 access_token（无需鉴权） |
| GET | `/v1/openapi/userinfo` | 获取用户信息（无需鉴权） |
| GET | `/v1/openapi/authcode` | 获取授权码（需鉴权） |

---

## 文件

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/file/preview/*path` | 预览/下载文件（无需鉴权） |
| GET | `/v1/file/upload` | 获取上传路径（预签名 URL） |
| POST | `/v1/file/upload` | 上传文件 |

---

## 通用

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/v1/common/appversion/:os/:version` | 最新版本（无需鉴权） |
| GET | `/v1/common/appversion/list` | 版本列表（无需鉴权） |
| GET | `/v1/common/chatbg` | 聊天背景列表（无需鉴权） |
| GET | `/v1/common/appmodule` | App 模块列表（无需鉴权） |
| GET | `/v1/common/countries` | 国家列表（无需鉴权） |
| GET | `/v1/common/appconfig` | App 全局配置（无需鉴权） |
| GET | `/v1/common/updater/:os/:version` | 版本更新检查（Tauri，无需鉴权） |
| GET | `/v1/common/pcupdater/:os` | PC 端更新检查（无需鉴权） |
| GET | `/v1/health` | 健康检查（无需鉴权） |

---

## 相关文档

- [[Bot-API]] — Bot 专用 API
- [[管理API]] — 管理员 API

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿，修正 Space API 路径为 /v1/space/（无 's'） |
