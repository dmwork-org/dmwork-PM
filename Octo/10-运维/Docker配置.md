---
title: Docker 配置
type: guide
project: octo
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, devops, docker, compose, environment]
---

# Docker 配置

[[部署架构|← 返回部署架构]]

## docker-compose.yml 结构

```yaml
version: "3.8"

services:
  # 业务逻辑层
  dmworkim:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:8080"    # HTTP API
      - "8081:8081"    # gRPC Webhook
    environment:
      - MODE=release
      - MYSQL_DSN=root:password@tcp(mysql:3306)/dmwork?charset=utf8mb4&parseTime=True
      - REDIS_ADDR=redis:6379
      - IM_URL=http://wukongim:5001
      - IM_WEBHOOK_GRPC_ADDR=:8081
    depends_on:
      - mysql
      - redis
      - wukongim
    volumes:
      - ./configs:/app/configs:ro
    restart: unless-stopped

  # 底层通讯层
  wukongim:
    image: wukongim/wukongim:latest
    ports:
      - "5200:5200"    # WebSocket（客户端连接）
      - "5001:5001"    # HTTP API
    environment:
      - IM_REDIS_ADDR=redis:6379
      - IM_DATASOURCE_ADDR=http://dmworkim:8080/v1/datasource
      - IM_WEBHOOK_ADDR=grpc://dmworkim:8081
    depends_on:
      - redis
    restart: unless-stopped

  # 数据库
  mysql:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=dmwork
    volumes:
      - mysql_data:/var/lib/mysql
    restart: unless-stopped

  # 缓存
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    restart: unless-stopped

  # 对象存储
  minio:
    image: minio/minio
    ports:
      - "9000:9000"    # API
      - "9001:9001"    # Console
    environment:
      - MINIO_ROOT_USER=minioadmin
      - MINIO_ROOT_PASSWORD=minioadmin
    command: server /data --console-address ":9001"
    volumes:
      - minio_data:/data
    restart: unless-stopped

volumes:
  mysql_data:
  redis_data:
  minio_data:
```

## Dockerfile（dmworkim）

```dockerfile
FROM golang:1.21-alpine AS builder

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o dmworkim ./main.go

FROM alpine:3.18
RUN apk --no-cache add ca-certificates tzdata
WORKDIR /app

COPY --from=builder /app/dmworkim .
COPY --from=builder /app/configs ./configs

EXPOSE 8080 8081
CMD ["./dmworkim"]
```

## 核心环境变量

### dmworkim

| 变量 | 默认值 | 说明 |
|------|--------|------|
| `MODE` | `debug` | 运行模式（`release` 或 `debug`） |
| `MYSQL_DSN` | — | MySQL 连接字符串 |
| `REDIS_ADDR` | `127.0.0.1:6379` | Redis 地址 |
| `IM_URL` | — | WuKongIM HTTP API 地址 |
| `IM_WEBHOOK_GRPC_ADDR` | `:8081` | gRPC Webhook 监听地址 |
| `FILE_SERVICE` | `minio` | 文件服务（minio/oss/seaweedfs） |
| `MINIO_ENDPOINT` | — | MinIO 地址 |
| `MINIO_ACCESS_KEY` | — | MinIO Access Key |
| `MINIO_SECRET_KEY` | — | MinIO Secret Key |
| `ADMIN_PWD` | — | 管理员初始密码 |
| `PHONE_SEARCH_OFF` | `false` | 关闭手机号搜索 |

> ⚠️ **重要**：`MODE` 的默认行为（无配置时）为 `debug` 模式（源码验证：`ConfigureWithViper()` 第527行 fallback 是 `DebugMode`），**生产环境必须显式设置 `MODE=release`**。

### configs/tsdd.yaml 主要配置项

```yaml
mode: "release"           # 运行模式

db:
  mysqlAddr: "root:password@tcp(mysql:3306)/dmwork"

redis:
  addr: "redis:6379"

im:
  apiURL: "http://wukongim:5001"
  webhookGRPCAddr: ":8081"

fileService: "minio"
minio:
  url: "http://minio:9000"
  accessKey: "minioadmin"
  secretKey: "minioadmin"
  bucket: "dmwork"
  regionID: ""

adminPwd: "your-admin-password"

register:
  off: false              # 是否关闭注册
  onlyChina: false        # 仅允许中国手机号
  usernameOn: true        # 允许用户名注册
  emailOn: true           # 允许邮箱注册
```

## 启动命令

```bash
# 构建并启动所有服务
docker compose up -d --build

# 查看日志
docker compose logs -f dmworkim
docker compose logs -f wukongim

# 停止
docker compose down

# 清理数据（危险！）
docker compose down -v
```

## 数据迁移

dmworkim 启动时自动执行 SQL 迁移（`rubenv/sql-migrate`），无需手动操作。迁移文件嵌入在二进制中（`//go:embed`）。

迁移按模块和文件名排序执行：
```
modules/user/sql/user-20210101-01.sql
modules/user/sql/user-20220101-01.sql
...
modules/group/sql/group-20220101-01.sql
...
```

## 相关文档

- [[部署架构]] — 整体架构
- [[故障排查]] — 常见问题

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿，特别注明 MODE=debug 的安全隐患 |
