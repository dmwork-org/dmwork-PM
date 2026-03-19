---
title: WuKongIM 二进制协议
type: reference
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, protocol, wukongim, binary, websocket]
---

# WuKongIM 二进制协议

`socket.ts`（两个适配器共用相同实现）是适配器的技术核心，从头实现了 WuKongIM 二进制协议的完整编解码。

## 协议数据包类型

```typescript
const enum PacketType {
  CONNECT    = 1,  // 客户端 → 服务器：建立连接请求
  CONNACK    = 2,  // 服务器 → 客户端：连接确认
  SEND       = 3,  // 客户端 → 服务器：发送消息（本实现不用，走 REST API）
  SENDACK    = 4,  // 服务器 → 客户端：发送确认
  RECV       = 5,  // 服务器 → 客户端：接收消息（核心！）
  RECVACK    = 6,  // 客户端 → 服务器：消息确认（QoS 保证）
  PING       = 7,  // 客户端 → 服务器：心跳
  PONG       = 8,  // 服务器 → 客户端：心跳响应
  DISCONNECT = 9,  // 服务器 → 客户端：断开连接
}

const PROTO_VERSION = 4;  // 使用第 4 版协议
```

## 数据包帧格式

```
┌───────────┬────────────────────────┬─────────────────────────────┐
│  Fixed    │  Variable-Length        │  Packet Body                │
│  Header   │  Remaining Length       │                             │
│  (1 byte) │  (1-4 bytes, MQTT风格) │  (remainingLength bytes)    │
└───────────┴────────────────────────┴─────────────────────────────┘
```

**Fixed Header（1字节）**：
- 高 4 位：PacketType（0-9）
- 低 4 位：标志位（根据包类型含义不同）

**Variable-Length（可变长度编码，MQTT 标准）**：
- 每字节低 7 位存储长度数值
- 最高位 = 1 表示后面还有字节
- 最多 4 字节，支持最大 268MB 包

> ⚠️ PONG 包特殊：单字节（无 Remaining Length 字段）

## 二进制编解码器

### Encoder 类

```typescript
class Encoder {
  writeByte(b: number)         // 1字节
  writeBytes(b: number[])      // 多字节
  writeInt16(b: number)        // 2字节大端序
  writeInt32(b: number)        // 4字节大端序
  writeInt64(n: bigint)        // 8字节大端序（BigInt）
  writeString(s: string)       // Int16(长度) + UTF-8字节
  toUint8Array(): Uint8Array
}
```

### Decoder 类

```typescript
class Decoder {
  readByte(): number
  readInt16(): number
  readInt32(): number          // 无符号（>>> 0）
  readInt64String(): string    // 读8字节返回十进制字符串（避免 JS 精度丢失）
  readInt64BigInt(): bigint
  readString(): string         // readInt16(长度) + UTF-8解码
  readRemaining(): Uint8Array  // 读剩余所有字节（payload）
  readVariableLength(): number // MQTT 可变长度解码
}
```

> **字符串编码**：使用 `unescape(encodeURIComponent(str))` 确保正确处理中文（避免直接 `charCodeAt` 截断多字节字符）

## RECV 包体结构

RECV 包按顺序读取以下字段：

```
[settingByte]     ← 位字段（receipt/topic/streamOn）
[msgKey]          ← string（消息签名key）
[fromUID]         ← string
[channelID]       ← string
[channelType]     ← byte
[expire]          ← int32（v3+）
[clientMsgNo]     ← string
[messageID]       ← int64（8字节，全局唯一ID）
[messageSeq]      ← int32（频道内序列号）
[timestamp]       ← int32（秒级）
[topic]           ← string（如果 setting.topic=true）
[encryptedPayload]← 剩余字节（AES-CBC 加密的 JSON payload）
```

## 粘包处理

```typescript
// 用 tempBuffer 积累接收到的字节，循环解包直到不够一个完整包
private handleRawData(data: Uint8Array): void {
  // 将新数据追加到缓冲区
  // 循环调用 unpackOne() 直到返回 null（数据不足）
}

private unpackOne(data: number[]): number[] {
  // 解出一个完整包（读 Fixed Header + Variable Length + Body）
  // 返回剩余未处理的字节
}
```

## 心跳机制

```typescript
// 心跳：60秒一次 PING，连续3次无 PONG 则强制重连
private restartHeart(): void {
  setInterval(() => sendRaw(encodePingPacket()), 60000)
}
```

## 断线重连

```typescript
// 断线重连：3秒后重试
private scheduleReconnect(): void {
  setTimeout(doConnect, 3000)
}

// 凭证更新：token 刷新后更新，下次重连时使用新凭证
updateCredentials(uid: string, token: string): void
```

## 相关页面

- [[DH密钥交换与加密]] — 加密握手详解
- [[流式消息协议]] — 流式消息处理
- [[../消息处理/入站与出站]] — 完整消息处理管道

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
