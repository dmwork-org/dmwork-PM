---
title: common 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, common, appconfig, version]
---

# common 模块

## 功能职责

通用配置与基础设施模块，提供：
- App 版本管理（发布/查询最新版本）
- 聊天背景管理
- App 模块配置（控制前端功能开关）
- 国家/地区列表
- App 全局配置查询
- PC/桌面端版本更新检查（Tauri 客户端兼容）
- 健康检查端点
- 短号服务（内部工具，无独立 API）

## API 端点表

### 用户端接口

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/common/appversion` | 添加 APP 版本 | 用户 JWT |
| GET | `/v1/common/appversion/:os/:version` | 获取最新版本 | 用户 JWT |
| GET | `/v1/common/appversion/list` | 版本列表 | 用户 JWT |
| GET | `/v1/common/chatbg` | 聊天背景列表 | 用户 JWT |
| GET | `/v1/common/appmodule` | App 模块列表 | 用户 JWT |
| GET | `/v1/common/countries` | 国家列表 | 无 |
| GET | `/v1/common/appconfig` | App 配置 | 无 |
| GET | `/v1/common/updater/:os/:version` | 版本更新检查（Tauri） | 无 |
| GET | `/v1/common/pcupdater/:os` | PC 端版本检查 | 无 |
| GET | `/v1/health` | 健康检查 | 无 |

### 管理员接口

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `/v1/manager/common/appconfig` | 获取 App 配置 | 管理员 |
| POST | `/v1/manager/common/appconfig` | 修改 App 配置 | 管理员 |
| GET | `/v1/manager/common/appmodule` | 获取 App 模块列表 | 管理员 |
| PUT | `/v1/manager/common/appmodule` | 修改 App 模块 | 管理员 |
| POST | `/v1/manager/common/appmodule` | 新增 App 模块 | 管理员 |
| DELETE | `/v1/manager/common/:sid/appmodule` | 删除 App 模块 | 管理员 |

## 关键数据模型

### app_config 表（全局配置核心）

| 字段 | 说明 |
|------|------|
| `rsa_private_key` / `rsa_public_key` | 系统 RSA 密钥对 |
| `super_token` / `super_token_on` | 超级 Token 及开关 |
| `revoke_second` | 消息可撤回时长（秒） |
| `welcome_message` | 登录欢迎语 |
| `new_user_join_system_group` | 注册用户是否默认加入系统群 |
| `search_by_phone` | 是否可通过手机号搜索 |
| `register_invite_on` | 注册邀请开关 |
| `channel_pinned_message_max_count` | 频道最多置顶消息数（默认 10） |
| `can_modify_api_url` | 是否允许客户端修改服务器地址 |

### app_version 表

| 字段 | 说明 |
|------|------|
| `app_version` | 版本号 |
| `os` | 操作系统（ios / android） |
| `is_force` | 是否强制升级 |
| `download_url` | 下载地址 |
| `signature` | 二进制包签名 |

### app_module 表

| 字段 | 说明 |
|------|------|
| `sid` | 模块 ID（如 base, login, rtc） |
| `name` | 模块名称 |
| `status` | 0=不可用 1=可用 2=基础模块 |

## 相关数据库表

- `app_config` — 全局系统配置
- `app_version` — 版本管理
- `app_module` — 功能模块开关
- `chat_bg` — 聊天背景图库
- `seq` — 序号管理（发号器）
- `shortno` — 短编号池（注：SQL 在 user 模块，服务封装在 common）

## 相关模块

- [[user]] — 短号服务使用方
- [[base]] — App 信息查询

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建，补充管理员 API |
