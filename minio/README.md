# MinIO 学习笔记

本仓库用于学习 [MinIO](https://min.io/) 的特性、部署方法及常用用法，适合自学和快速查阅。

## 目录

- [MinIO 简介](#minio-简介)
- [S3 API 简介](#s3-api-简介)
- [核心特性](#核心特性)
- [部署 MinIO](#部署-minio)
  - [本地部署](#本地部署)
  - [Docker 部署](#docker-部署)
- [常用操作示例](#常用操作示例)
  - [创建 Bucket](#创建-bucket)
  - [上传/下载文件](#上传下载文件)
  - [使用 SDK](#使用-sdk)
- [常见问题](#常见问题)
- [常见问题解决](#常见问题解决)
- [参考资料](#参考资料)

---

## MinIO 简介

MinIO 是一款高性能、分布式的对象存储服务，兼容 Amazon S3 API。适用于存储海量非结构化数据，如图片、视频、备份等。

## S3 API 简介

S3 API 是指 Amazon Simple Storage Service（Amazon S3）提供的对象存储服务的应用程序编程接口（API）。

### 详细解释

**Amazon S3** 是亚马逊云（AWS）提供的对象存储服务，广泛用于存储和检索任意数量的数据。S3 API 就是 Amazon S3 提供的一套标准接口，允许开发者通过 HTTP 协议对存储桶（Bucket）和对象（Object）进行操作。

#### S3 API 的主要特点

- **RESTful 风格**：基于 HTTP/HTTPS 协议，支持 GET、PUT、POST、DELETE 等常用方法。
- **操作对象存储**：可以创建/删除 Bucket，上传/下载/删除文件，设置访问权限等。
- **广泛兼容**：许多第三方对象存储（如 MinIO、阿里云 OSS、腾讯云 COS 等）都兼容 S3 API，这意味着你可以用同样的代码访问不同厂商的对象存储服务。

#### 常见的 S3 API 操作

- 创建 Bucket：`PUT /{bucket}`
- 上传文件：`PUT /{bucket}/{object}`
- 下载文件：`GET /{bucket}/{object}`
- 列出文件：`GET /{bucket}`
- 删除文件：`DELETE /{bucket}/{object}`

#### 为什么 S3 API 重要？

- **标准统一**：S3 API 已成为对象存储领域的事实标准。
- **生态丰富**：大量 SDK、工具、第三方服务都支持 S3 API。
- **易于迁移**：支持 S3 API 的存储服务之间迁移和切换成本低。

#### 例子

比如 Python 的 boto3、JavaScript 的 aws-sdk、Go 的 minio-go 等 SDK，底层其实都是通过 S3 API 与对象存储服务通信。

---

## 核心特性

- 100% 兼容 S3 API
- 支持分布式部署和高可用
- 高性能读写
- 简单易用的 Web 管理界面
- 支持多种客户端 SDK（Go、Python、JavaScript 等）

## 部署 MinIO

### 本地部署

1. [下载 MinIO](https://min.io/download#/macos)
2. 启动服务：

   ```bash
   ./minio server /data
   ```

3. 默认管理界面地址: [http://localhost:9000](http://localhost:9000)

### Docker 部署

```bash
docker run -p 9000:9000 -p 9001:9001 \
  -e "MINIO_ROOT_USER=minioadmin" \
  -e "MINIO_ROOT_PASSWORD=minioadmin" \
  -v /your/data/path:/data \
  minio/minio server /data --console-address ":9001"
```

## 常用操作示例

### 创建 Bucket

```bash
mc mb myminio/mybucket
```

### 上传/下载文件

```bash
mc cp ./localfile.txt myminio/mybucket/
mc cp myminio/mybucket/localfile.txt ./
```

### 使用 SDK

以 Python 为例：

```python
from minio import Minio

client = Minio(
    "localhost:9000",
    access_key="minioadmin",
    secret_key="minioadmin",
    secure=False
)

client.fput_object("mybucket", "hello.txt", "hello.txt")
```

## 常见问题

- 端口冲突：确保 9000/9001 端口未被占用
- 权限问题：检查数据目录权限
- 访问控制：合理配置用户和策略

## 常见问题解决

### 问题：INFO  ==> ** Starting MinIO setup **
/opt/bitnami/scripts/libminio.sh: line 324: /data/.root_user: Permission denied

**解决办法：**
1. 赋予数据目录正确权限：
   ```bash
   sudo chown -R 1000:1000 minio_data
   chmod 777 minio_data/
   ```
2. 重新启动 MinIO 容器。

## 参考资料

- [MinIO 官方文档](https://docs.min.io/)
- [MinIO Github](https://github.com/minio/minio)
- [MinIO Client (mc) 文档](https://docs.min.io/docs/minio-client-quickstart-guide.html)


