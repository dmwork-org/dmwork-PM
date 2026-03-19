---
title: Octo Wiki 变更记录
type: changelog
project: octo
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, changelog]
---

# Octo Wiki 变更记录

## 0.1.0 (2026-03-19)

### 重大变更

- 按 arc42 + Diátaxis 规范重组全部文档结构
- 从"按仓库组织"改为"按关注点组织"（产品/架构/开发/服务端/适配层/客户端/框架/API/数据库/运维）

### 新增

- **产品层**：愿景与定位、用户角色与场景、术语表、路线图
- **架构决策记录（ADR）**：双层架构、Space前缀约定、Bot-Token体系
- **横切关注点**：安全与加密、Bot系统、Space多租户、推送系统
- **开发指南**：快速开始、开发环境搭建、编码规范
- **运维文档**：部署架构、Docker配置、故障排查
- **统一API参考**：Bot-API、用户API、管理API
- **统一数据库文档**：概览、表结构、关系图、消息分片、Bot数据模型
- **客户端层**：客户端概述、Web/iOS/Android 各端详情、Web组件/Service/Space
- **适配层**：入站与出站消息处理管道

### 修正（基于源码验证）

- **Space API路径**：`/v1/spaces/` → `/v1/space/`（无复数 s）
- **BotFather Apply路由**：`/v1/bot/apply` → `/v1/robot/apply`
- **getChannelMessages**：`GET` → `POST /v1/bot/messages/sync`
- **BotRegisterResp**：补充 `owner_channel_id` 字段
- **ContentType=16**：补充 `InviteJoinOrganization` 类型
- **cache.Cache 接口签名**：修正参数类型
- **iOS 最低版本**：14.0（非 13.0）
- **iOS productName**：`LiMaoIMDemo`（遗留历史品牌，非 DMWork）
- **iOS Bugly**：通过 `vendored_frameworks` 引入，非 CocoaPods
- **OpenClaw 技能数**：54（非 53）
- **npm 包目录**：无 `src/` 目录（ESM 直接发布 TypeScript）
- **Android MyLibs:rlottie**：补充此 Gradle 模块（Lottie 动画）
- **Android rootProject.name**：`TsddClient`（遗留历史品牌）
- **RTC 消息类型**：补充 9989-9994（RTC start/end/reject/cancel/busy/missed）

### 文档规范

- 统一 YAML frontmatter（`version` / `type` / `status` / `tags`）
- 每页内置 CHANGELOG 表
- Mermaid 图表 + wikilinks 互链
- 按 Diátaxis 框架区分四类文档：Concept / Reference / Guide / Tutorial

---

## 已建立文档索引

### 01-产品

| 文件 | 状态 |
|------|------|
| [[愿景与定位]] | ✅ |
| [[用户角色与场景]] | ✅ |
| [[术语表]] | ✅ |
| [[路线图]] | ✅ |

### 02-架构

| 文件 | 状态 |
|------|------|
| [[架构概述]] | ✅ |
| [[上下文与边界]] | ✅ |
| [[构建块视图]] | ✅ |
| [[运行时视图]] | ✅ |
| [[部署视图]] | ✅ |
| [[决策记录/ADR-001-双层架构]] | ✅ |
| [[决策记录/ADR-002-Space前缀约定]] | ✅ |
| [[决策记录/ADR-003-Bot-Token体系]] | ✅ |
| [[横切关注点/安全与加密]] | ✅ |
| [[横切关注点/Bot系统]] | ✅ |
| [[横切关注点/Space多租户]] | ✅ |
| [[横切关注点/推送系统]] | ✅ |

### 03-开发指南

| 文件 | 状态 |
|------|------|
| [[快速开始]] | ✅ |
| [[开发环境搭建]] | ✅ |
| [[编码规范]] | ✅ |

### 04-服务端

| 文件 | 状态 |
|------|------|
| [[04-服务端/概述]] | ✅ |
| [[04-服务端/WuKongIM集成]] | ✅ |
| [[04-服务端/核心库/概述]] | ✅ |
| [[04-服务端/模块/base]] | ✅ |
| [[04-服务端/模块/botfather]] | ✅ |
| [[04-服务端/模块/channel]] | ✅ |
| [[04-服务端/模块/common]] | ✅ |
| [[04-服务端/模块/file]] | ✅ |
| [[04-服务端/模块/group]] | ✅ |
| [[04-服务端/模块/message]] | ✅ |
| [[04-服务端/模块/openapi]] | ✅ |
| [[04-服务端/模块/qrcode]] | ✅ |
| [[04-服务端/模块/report]] | ✅ |
| [[04-服务端/模块/robot]] | ✅ |
| [[04-服务端/模块/search]] | ✅ |
| [[04-服务端/模块/source]] | ✅ |
| [[04-服务端/模块/space]] | ✅ |
| [[04-服务端/模块/statistics]] | ✅ |
| [[04-服务端/模块/user]] | ✅ |
| [[04-服务端/模块/webhook]] | ✅ |
| [[04-服务端/模块/workplace]] | ✅ |

### 05-适配层

| 文件 | 状态 |
|------|------|
| [[05-适配层/概述]] | ✅ |
| [[OpenClaw适配器]] | ✅ |
| [[Claude-Code适配器]] | ✅ |
| [[协议/WuKongIM二进制协议]] | ✅ |
| [[协议/DH密钥交换与加密]] | ✅ |
| [[协议/流式消息协议]] | ✅ |
| [[消息处理/入站与出站]] | ✅ |

### 06-客户端

| 文件 | 状态 |
|------|------|
| [[06-客户端/概述]] | ✅ |
| [[Web/概述]] | ✅ |
| [[Web/组件与消息渲染]] | ✅ |
| [[Web/Service层与Space]] | ✅ |
| [[iOS/概述]] | ✅ |
| [[Android/概述]] | ✅ |
| [[Android/推送系统]] | ✅ |

### 07-OpenClaw框架

| 文件 | 状态 |
|------|------|
| [[07-OpenClaw框架/概述]] | ✅ |
| [[Gateway]] | ✅ |
| [[Channel系统]] | ✅ |
| [[Plugin系统]] | ✅ |
| [[Provider体系]] | ✅ |
| [[Session管理]] | ✅ |
| [[ACP协作协议]] | ✅ |
| [[Agent-Workspace]] | ✅ |
| [[DMWork集成]] | ✅ |
| [[Skill与Extension生态]] | ✅ |

### 08-API参考

| 文件 | 状态 |
|------|------|
| [[Bot-API]] | ✅ |
| [[用户API]] | ✅ |
| [[管理API]] | ✅ |

### 09-数据库

| 文件 | 状态 |
|------|------|
| [[09-数据库/概览]] | ✅ |
| [[表结构详情]] | ✅ |
| [[表关系图]] | ✅ |
| [[消息分片设计]] | ✅ |
| [[Bot数据模型]] | ✅ |

### 10-运维

| 文件 | 状态 |
|------|------|
| [[部署架构]] | ✅ |
| [[Docker配置]] | ✅ |
| [[故障排查]] | ✅ |
