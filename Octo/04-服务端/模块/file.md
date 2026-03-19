---
title: file 模块
type: reference
project: dmworkim
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, module, file, storage, oss, minio]
---

# file 模块

## 功能职责

文件存储与访问模块，通过统一抽象接口支持多种存储后端：

| 存储后端 | 说明 |
|---------|------|
| **MinIO** | 自托管对象存储（`minio/minio-go/v7`） |
| **阿里云 OSS** | `aliyun/aliyun-oss-go-sdk` |
| **SeaweedFS** | 分布式文件系统（HTTP REST） |
| **七牛云** | `qiniu/go-sdk/v7` |
| **腾讯云 COS** | `cos-go-sdk-v5` |

通过配置切换存储后端，对上层模块透明。

## API 端点表

| 方法 | 路径 | 描述 | 鉴权 |
|------|------|------|------|
| GET | `/v1/file/preview/*path` | 预览/下载文件 | 无 |
| GET | `/v1/file/upload` | 获取上传路径（预签名 URL） | 用户 JWT |
| POST | `/v1/file/upload` | 上传文件 | 用户 JWT |

## 服务接口

```go
// 上传服务接口
type IUploadService interface {
    UploadFile(filePath, contentType string, copyFunc) (map[string]interface{}, error)
    DownloadURL(path, filename string) (string, error)
    GetFile(path string) (io.ReadCloser, contentType string, error)  // MinIO 私有 bucket 直读
}

// 完整服务接口（扩展 IUploadService）
type IService interface {
    IUploadService
    // 文件上传、预览、URL 生成、图像处理等
}
```

## 图像处理

支持图像处理（go 图像库 + `disintegration/imaging`）：
- 图片缩放/裁剪
- 头像生成
- 缩略图

## 相关模块

- [[botfather]] — Bot 文件上传（/v1/bot/file/upload）
- [[robot]] — Robot 文件代理
- [[group]] — 群头像上传
- [[user]] — 用户头像上传

---

## CHANGELOG

| 版本 | 日期 | 作者 | 变更 |
|------|------|------|------|
| 0.1.0 | 2026-03-19 | 戏精 | 初始创建 |
