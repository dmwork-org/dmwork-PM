---
title: report 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, report]
---

# report 模块

## 功能职责

内容举报系统，支持用户举报违规内容，提供管理员后台查看和处理举报。

## API 端点表

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `/v1/report/categories` | 举报分类列表 | 用户 JWT |
| GET | `/v1/report/html` | 举报 HTML 页面 | 用户 JWT |
| POST | `/v1/report/session/resolve` | 解决举报会话 | 用户 JWT |
| POST | `/v1/report` | 提交举报 | 用户 JWT |
| GET | `/v1/report/list` | 举报列表 | 管理员 |

## 关键数据模型

### report_category 表

| 字段 | 说明 |
|------|------|
| `category_no` | 类别编号（唯一） |
| `category_name` | 类别中文名称 |
| `category_ename` | 类别英文名称 |
| `parent_category_no` | 父类别编号（空=顶级） |

### report 表

| 字段 | 说明 |
|------|------|
| `uid` | 举报用户 uid |
| `category_no` | 举报类别编号 |
| `channel_id` | 被举报频道 ID |
| `channel_type` | 被举报频道类型 |
| `imgs` | 证据图片集合（逗号分隔） |
| `remark` | 举报备注 |

## 相关数据库表

- `report_category` — 举报分类（支持多级）
- `report` — 举报记录

## 相关模块

- [[user]] — 举报者身份验证

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
