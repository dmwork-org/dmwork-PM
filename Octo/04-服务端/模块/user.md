---
title: user 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, user, auth, friend, device]
---

# user 模块

## 功能职责

用户系统核心模块，功能最为全面，覆盖：
- **注册/登录**：手机号短信、用户名、邮箱、OAuth（Gitee / GitHub）、Web3 钱包签名
- **用户信息管理**：头像、昵称、设置
- **设备管理**：注册/注销推送 Token、设备列表、徽章数
- **好友系统**：申请/确认/拒绝/删除/同步/搜索/备注
- **黑名单**
- **安全**：聊天密码、锁屏密码、登录密码修改/重置
- **账号注销**
- **在线状态**
- **通讯录（MailList）**
- **红点（RedDot）系统**
- **授权登录**（grant_login）/ PC 登录
- **Web3 公钥上传与验签**
- **管理员后台**：用户列表、管理员管理、封禁/解禁、设备查询

## API 端点表

### 注册与登录

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/user/register` | 注册（手机号） | 无 |
| POST | `/v1/user/login` | 登录（手机号+短信） | 无 |
| POST | `/v1/user/usernamelogin` | 用户名密码登录 | 无 |
| POST | `/v1/user/usernameregister` | 用户名注册 | 无 |
| POST | `/v1/user/emaillogin` | 邮箱登录 | 无 |
| POST | `/v1/user/emailregister` | 邮箱注册 | 无 |
| POST | `/v1/user/email/sendcode` | 发送邮箱验证码 | 无 |
| POST | `/v1/user/email/forgetpwd` | 邮箱忘记密码 | 无 |
| GET | `/v1/user/web3verifytext` | 获取 Web3 验签文本 | 无 |
| POST | `/v1/user/web3verifysign` | Web3 签名验证 | 无 |
| POST | `/v1/user/pwdforget_web3` | Web3 重置密码 | 无 |
| POST | `/v1/user/sms/forgetpwd` | 短信找回密码 | 无 |
| POST | `/v1/user/pwdforget` | 重置密码 | 无 |
| POST | `/v1/user/quit` | 退出登录 | 用户 JWT |

### 用户信息

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `/v1/user/search` | 搜索用户 | 用户 JWT |
| GET | `/v1/users/:uid/avatar` | 用户头像 | 无 |
| GET | `/v1/users/:uid` | 获取用户信息 | 用户 JWT |
| POST | `/v1/users/:uid/avatar` | 上传头像 | 用户 JWT |
| PUT | `/v1/users/:uid/setting` | 更新用户设置 | 用户 JWT |
| PUT | `/v1/user/current` | 修改用户信息 | 用户 JWT |
| GET | `/v1/user/qrcode` | 我的二维码 | 用户 JWT |
| PUT | `/v1/user/my/setting` | 更新我的设置 | 用户 JWT |
| PUT | `/v1/user/updatepassword` | 修改密码 | 用户 JWT |
| POST | `/v1/user/web3publickey` | 上传 Web3 公钥 | 用户 JWT |
| DELETE | `/v1/user/destroy/:code` | 注销账号 | 用户 JWT |
| POST | `/v1/user/sms/destroy` | 注销验证码 | 用户 JWT |

### 设备与推送

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/user/device_token` | 注册推送设备 | 用户 JWT |
| DELETE | `/v1/user/device_token` | 注销推送设备 | 用户 JWT |
| POST | `/v1/user/device_badge` | 更新设备徽章数 | 用户 JWT |
| GET | `/v1/user/devices` | 设备列表 | 用户 JWT |
| DELETE | `/v1/user/devices/:device_id` | 删除设备 | 用户 JWT |
| GET | `/v1/user/online` | 在线设备列表 | 用户 JWT |
| POST | `/v1/user/online` | 批量查询在线状态 | 用户 JWT |
| POST | `/v1/user/pc/quit` | 退出 PC 登录 | 用户 JWT |

### 好友系统

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/user/friend/apply` | 好友申请 | 用户 JWT |
| GET | `/v1/user/friend/apply` | 好友申请列表 | 用户 JWT |
| DELETE | `/v1/user/friend/apply/:to_uid` | 删除好友申请 | 用户 JWT |
| PUT | `/v1/user/friend/refuse/:to_uid` | 拒绝好友申请 | 用户 JWT |
| POST | `/v1/user/friend/sure` | 好友确认 | 用户 JWT |
| GET | `/v1/user/friend/sync` | 同步好友 | 用户 JWT |
| GET | `/v1/user/friend/search` | 搜索好友 | 用户 JWT |
| PUT | `/v1/user/friend/remark` | 好友备注 | 用户 JWT |
| DELETE | `/v1/user/friends/:uid` | 删除好友 | 用户 JWT |

### 安全与其他

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/user/blacklist/:uid` | 添加黑名单 | 用户 JWT |
| DELETE | `/v1/user/blacklist/:uid` | 移除黑名单 | 用户 JWT |
| GET | `/v1/user/blacklists` | 黑名单列表 | 用户 JWT |
| POST | `/v1/user/chatpwd` | 设置聊天密码 | 用户 JWT |
| POST | `/v1/user/lockscreenpwd` | 设置锁屏密码 | 用户 JWT |
| PUT | `/v1/user/lock_after_minute` | 设置锁屏时间 | 用户 JWT |
| DELETE | `/v1/user/lockscreenpwd` | 关闭锁屏 | 用户 JWT |
| GET | `/v1/user/grant_login` | 授权登录 | 用户 JWT |
| GET | `/v1/user/customerservices` | 客服列表 | 用户 JWT |
| POST | `/v1/user/maillist` | 添加通讯录 | 用户 JWT |
| GET | `/v1/user/maillist` | 获取通讯录 | 用户 JWT |
| GET | `/v1/user/reddot/:category` | 获取红点 | 用户 JWT |
| DELETE | `/v1/user/reddot/:category` | 清除红点 | 用户 JWT |

### 管理员接口

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/manager/login` | 管理员登录 | 无 |
| POST | `/v1/manager/user/admin` | 添加管理员 | 管理员 |
| GET | `/v1/manager/user/admin` | 查询管理员 | 管理员 |
| DELETE | `/v1/manager/user/admin` | 删除管理员 | 管理员 |
| POST | `/v1/manager/user/add` | 添加用户 | 管理员 |
| POST | `/v1/manager/user/resetpassword` | 重置用户密码 | 管理员 |
| GET | `/v1/manager/user/list` | 用户列表 | 管理员 |
| GET | `/v1/manager/user/friends` | 某用户好友列表 | 管理员 |
| GET | `/v1/manager/user/blacklist` | 黑名单列表 | 管理员 |
| GET | `/v1/manager/user/disablelist` | 封禁用户列表 | 管理员 |
| GET | `/v1/manager/user/online` | 在线设备信息 | 管理员 |
| PUT | `/v1/manager/user/liftban/:uid/:status` | 封禁/解禁用户 | 管理员 |
| POST | `/v1/manager/user/updatepassword` | 修改用户密码 | 管理员 |
| GET | `/v1/manager/user/devices` | 查看用户设备 | 管理员 |

## 核心数据模型

### user 表（部分关键字段）

| 字段 | 说明 |
|------|------|
| `uid` | 用户唯一 ID（UNIQUE） |
| `short_no` | 短编码（搜索用） |
| `robot` | 0=人类 1=机器人 |
| `role` | 用户角色（admin / superAdmin） |
| `category` | 用户分类（service=客服 system=系统） |
| `password` | bcrypt 哈希（最长 255 字符） |
| `status` | 0=禁用 1=可用 |
| `is_destroy` | 是否已注销 |
| `web3_public_key` | Web3 公钥 |
| `msg_expire_second` | 消息过期时长（秒） |

### IService 接口

user.IService 是**被依赖最广泛**的服务接口，提供 30+ 方法：

```go
GetUser(uid string) (*Resp, error)
GetUserDetail(uid string) (*DetailResp, error)
GetUsers(uids []string) ([]*Resp, error)
GetFriends(uid string) ([]*FriendResp, error)
AddFriend(uid, toUID string) error
IsFriend(uid, toUID string) (bool, error)
GetUserOnlineStatus(uids []string) ([]*OnlineStatusResp, error)
UpdateUser(uid string, data map[string]interface{}) error
ExistBlacklist(uid, toUID string) (bool, error)
// ...30+ 方法
```

## 相关数据库表

- `user` — 用户主表
- `user_setting` — 用户对他人的个性化设置
- `friend` — 好友关系
- `friend_apply_record` — 好友申请
- `device` — 登录设备
- `user_online` — 在线状态
- `user_maillist` — 手机通讯录
- `user_red_dot` — 红点系统
- `login_log` — 登录日志
- `signal_identities` / `signal_onetime_prekeys` — E2E 加密
- `gitee_user` / `github_user` — 三方登录
- `shortno` — 短编号池
- `device_flag` — 设备类型定义

## 相关模块

- [[group]] — group.creator → user.uid
- [[robot]] — robot 本质是 robot=1 的特殊 user
- [[space]] — space.creator → user.uid
- [[message]] — 消息发送者/接收者
- [[webhook]] — 推送时查询用户设备 Token

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
