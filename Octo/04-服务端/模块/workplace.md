---
title: workplace 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, workplace, miniapp]
---

# workplace 模块

## 功能职责

工作台模块，类似企业微信"应用"功能，提供小程序/应用的管理和展示：
- 横幅（Banner）管理
- 应用（App）的添加/删除/排序
- 使用记录跟踪（常用 App 统计）
- 分类（Category）管理
- 管理员后台：完整的应用、分类、横幅 CRUD

## API 端点表

### 用户端接口

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `/v1/workplace/banner` | 获取横幅 | 用户 JWT |
| PUT | `/v1/workplace/app/reorder` | 排序 App | 用户 JWT |
| GET | `/v1/workplace/app/record` | 查询常用 App | 用户 JWT |
| GET | `/v1/workplace/app` | 获取用户已添加 App | 用户 JWT |
| POST | `/v1/workplace/apps/:app_id` | 添加 App | 用户 JWT |
| DELETE | `/v1/workplace/apps/:app_id` | 删除 App | 用户 JWT |
| POST | `/v1/workplace/apps/:app_id/record` | 添加使用记录 | 用户 JWT |
| DELETE | `/v1/workplace/apps/:app_id/record` | 删除使用记录 | 用户 JWT |
| GET | `/v1/workplace/category` | 获取分类 | 用户 JWT |
| GET | `/v1/workplace/categorys/:category_no/app` | 获取分类下 App | 用户 JWT |

### 管理员接口

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/manager/workplace/category` | 添加分类 | 管理员 |
| GET | `/v1/manager/workplace/category` | 获取分类 | 管理员 |
| PUT | `/v1/manager/workplace/category/reorder` | 排序分类 | 管理员 |
| DELETE | `/v1/manager/workplace/categorys/:category_no` | 删除分类 | 管理员 |
| PUT | `/v1/manager/workplace/categorys/:category_no` | 修改分类 | 管理员 |
| GET | `/v1/manager/workplace/categorys/:category_no/app` | 获取分类下 App | 管理员 |
| PUT | `/v1/manager/workplace/categorys/:category_no/app/reorder` | 排序分类 App | 管理员 |
| POST | `/v1/manager/workplace/categorys/:category_no/app` | 新增分类 App | 管理员 |
| DELETE | `/v1/manager/workplace/categorys/:category_no/apps/:app_id` | 删除分类 App | 管理员 |
| POST | `/v1/manager/workplace/app` | 添加 App | 管理员 |
| GET | `/v1/manager/workplace/app` | 获取 App 列表 | 管理员 |
| PUT | `/v1/manager/workplace/apps/:app_id` | 修改 App | 管理员 |
| DELETE | `/v1/manager/workplace/apps/:app_id` | 删除 App | 管理员 |
| POST | `/v1/manager/workplace/banner` | 添加横幅 | 管理员 |
| GET | `/v1/manager/workplace/banner` | 获取横幅列表 | 管理员 |
| DELETE | `/v1/manager/workplace/banners/:banner_no` | 删除横幅 | 管理员 |
| PUT | `/v1/manager/workplace/banners/:banner_no` | 修改横幅 | 管理员 |
| PUT | `/v1/manager/workplace/banner/reorder` | 排序横幅 | 管理员 |

## 关键数据模型

### workplace_app 表

| 字段 | 说明 |
|------|------|
| `app_id` | 应用 ID |
| `app_category` | 应用分类（机器人 / 客服） |
| `jump_type` | 打开方式：0=网页 1=原生 |
| `app_route` | App 端打开地址 |
| `web_route` | Web 端打开地址 |
| `is_paid_app` | 是否付费应用 |

### workplace_banner 表

| 字段 | 说明 |
|------|------|
| `cover` | 封面图地址 |
| `jump_type` | 打开方式 |
| `route` | 点击跳转地址 |
| `sort_num` | 排序号 |

## 相关数据库表

- `workplace_category` — 工作台分类
- `workplace_app` — 应用定义
- `workplace_user_app` — 用户收藏应用
- `workplace_banner` — 横幅
- `workplace_app_user_record` — 使用记录
- `workplace_category_app` — 分类-应用关联

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
