---
title: search 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, search, elasticsearch]
---

# search 模块

## 功能职责

全局搜索模块，聚合用户、群组、消息的跨域搜索能力，可选接入 Elasticsearch（通过 [[base|base/elastic]]）进行全文搜索增强。

## API 端点表

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| POST | `/v1/search/global` | 全局搜索 | 用户 JWT |

## 搜索参数

| 参数 | 说明 |
|------|------|
| `keyword` | 搜索关键词 |
| `only_message` | 是否只搜索消息 |
| `content_type` | 消息内容类型过滤 |
| `from_uid` | 按发送者过滤 |
| `channel_id` / `channel_type` | 按频道过滤 |
| `topic` | 话题过滤 |
| `limit` / `page` | 分页 |
| `start_time` / `end_time` | 时间范围 |

## 搜索结果聚合

search 模块依赖以下服务接口聚合多源数据：

| 数据源 | 服务接口 | 说明 |
|-------|---------|------|
| 用户 | `user.IService` | 搜索匹配的用户 |
| 群组 | `group.IService` | 搜索匹配的群组 |
| 消息 | `message.IService` | 搜索匹配的历史消息 |

## 相关模块

- [[user]] — 提供用户搜索 IService
- [[group]] — 提供群组搜索 IService
- [[message]] — 提供消息搜索 IService
- [[base]] — Elasticsearch 集成（base/elastic）

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
