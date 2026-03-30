# transport

## 功能概述

`transport/` 模块实现了 app-server 的传输层，负责客户端连接的建立、管理和数据收发。它抽象了两种传输方式——stdio（标准输入输出）和 WebSocket——使上层消息处理器无需关心底层通信细节。

该模块还包含 WebSocket 连接的认证子系统，支持基于能力令牌（Capability Token）和签名 JWT 承载令牌（Signed Bearer Token）两种认证模式。

## 目录结构

```
transport/
├── mod.rs                      # 模块入口，定义公共类型和消息路由
├── stdio.rs                    # stdio 传输实现
├── websocket.rs                # WebSocket 传输实现
└── auth.rs                     # WebSocket 认证子系统
```

## 核心接口/API

### mod.rs - 传输层核心

- **`AppServerTransport`** - 传输方式枚举：
  - `Stdio` - 单客户端 stdio 模式，默认监听 URL 为 `stdio://`
  - `WebSocket { bind_address }` - 多客户端 WebSocket 模式，监听 URL 格式为 `ws://IP:PORT`

- **`TransportEvent`** - 传输事件枚举，用于向处理器报告连接状态变化：
  - `ConnectionOpened` - 新连接建立，携带 `ConnectionId` 和写入通道
  - `ConnectionClosed` - 连接关闭
  - `IncomingMessage` - 收到入站 JSON-RPC 消息

- **`ConnectionState`** / **`OutboundConnectionState`** - 连接状态管理，追踪每个连接的初始化状态、实验性 API 开关和通知订阅偏好

- **`route_outgoing_envelope()`** - 出站消息路由函数，支持定向发送（`ToConnection`）和广播（`Broadcast`）两种模式

- **`CHANNEL_CAPACITY`** (128) - 有界通道容量，平衡吞吐量和内存使用

- **`forward_incoming_message()`** - 将原始文本反序列化为 JSON-RPC 消息并转发到处理器

- **`enqueue_incoming_message()`** - 入站消息入队，当队列满时对 Request 返回过载错误，对非 Request 类型消息阻塞等待

### stdio.rs - 标准 I/O 传输

- **`start_stdio_connection()`** - 启动 stdio 连接，创建两个异步任务：
  - 读取任务：从 stdin 按行读取 JSON-RPC 消息
  - 写入任务：将出站消息序列化后写入 stdout
  - 连接 ID 固定为 0（单客户端模式）

### websocket.rs - WebSocket 传输

- **`start_websocket_acceptor()`** - 启动 WebSocket 监听器，基于 `axum` 框架：
  - 路由 `/readyz` 和 `/healthz` 到健康检查端点
  - 所有其他路径处理 WebSocket 升级
  - 拒绝带有 `Origin` 头的请求（防御浏览器跨站攻击）
  - 支持优雅关闭（通过 `CancellationToken`）
  - 为每个 WebSocket 连接分配递增的 `ConnectionId`

- **`run_websocket_connection()`** - 运行单个 WebSocket 连接，拆分为独立的入站和出站循环任务

### auth.rs - WebSocket 认证

- **`AppServerWebsocketAuthSettings`** / **`AppServerWebsocketAuthArgs`** - 认证配置，支持两种模式：
  - `CapabilityToken` - 基于文件的能力令牌，通过 SHA-256 哈希进行常量时间比较
  - `SignedBearerToken` - JWT 签名承载令牌，使用 HMAC-SHA256 验证签名，支持 `exp`/`nbf`/`iss`/`aud` 声明校验和时钟偏差容忍

- **`authorize_upgrade(headers, policy) -> Result<(), WebsocketAuthError>`** - 在 WebSocket 握手时验证 `Authorization: Bearer <token>` 头

- **`policy_from_settings(settings) -> WebsocketAuthPolicy`** - 从配置构建运行时认证策略，包括读取密钥文件和验证密钥强度（最少 32 字节）
