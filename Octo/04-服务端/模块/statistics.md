---
title: statistics 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, statistics, analytics]
---

# statistics 模块

## 功能职责

数据统计后台模块，提供平台运营数据查询，需要管理员权限。

## API 端点表

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `/v1/statistics/countnum` | 统计总数量（用户数、群数等） | 管理员 |
| GET | `/v1/statistics/registeruser/:start_date/:end_date` | 区间注册用户统计 | 管理员 |
| GET | `/v1/statistics/createdgroup/:start_date/:end_date` | 区间建群统计 | 管理员 |

## 数据聚合

statistics 模块依赖以下服务接口获取统计数据：

| 数据 | 来源 |
|------|------|
| 用户数量/注册趋势 | `user.IService` |
| 群组数量/建群趋势 | `group.IService` |

## 相关模块

- [[user]] — 提供用户统计数据
- [[group]] — 提供群组统计数据

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
