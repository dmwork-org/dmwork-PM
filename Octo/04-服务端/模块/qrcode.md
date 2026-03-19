---
title: qrcode 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, qrcode]
---

# qrcode 模块

## 功能职责

二维码解析与路由处理模块，解析各类二维码（用户二维码、群组二维码、邀请二维码等），返回对应的业务数据和前端跳转指令。

> 注意：qrcode 是最简化的注册形式，没有设置 `Name` 字段，也没有 Swagger。

## API 端点表

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `{QRCodeInfoURL}` | 处理二维码信息（路径可通过配置项自定义） | — |

## 核心数据模型

```go
// 处理结果
HandleResult {
    Forward Forward                // 跳转方式（前端路由目标）
    Type    HandlerType            // 数据类型（用户/群组/邀请等）
    Data    map[string]interface{} // 具体业务数据
}
```

`constant.go` 定义了各类二维码类型（`HandlerType`）和跳转方式（`Forward`）。

## 支持的二维码类型

| 类型 | 说明 |
|------|------|
| 用户二维码 | 扫码后跳转用户主页 / 添加好友 |
| 群组二维码 | 扫码后跳转加入群组流程 |
| 邀请二维码 | 群邀请码，扫后确认加入 |

## 相关模块

- [[user]] — 用户二维码（/v1/user/qrcode）
- [[group]] — 群组二维码（/v1/groups/:id/qrcode）
- [[source]] — 数据提供者（qrcode 通过 source 获取用户/群组数据，避免循环依赖）

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
