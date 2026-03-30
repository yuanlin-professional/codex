# `transport/auth.rs` — WebSocket 传输层认证（Capability Token / Signed JWT）

## 功能概述

本模块实现了 WebSocket 传输层的认证机制，支持两种模式：Capability Token（基于共享令牌的 SHA-256 常数时间比较）和 Signed Bearer Token（基于 HMAC-SHA256 签名的 JWT 验证，支持 exp/nbf/iss/aud 声明和可配置的时钟偏移容忍）。认证策略通过 CLI 参数（`--ws-auth`、`--ws-token-file`、`--ws-shared-secret-file` 等）配置，在 WebSocket 升级时执行。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppServerWebsocketAuthArgs` | 结构体（clap） | WebSocket 认证 CLI 参数 |
| `AppServerWebsocketAuthSettings` | 结构体 | 解析后的认证配置 |
| `AppServerWebsocketAuthConfig` | 枚举 | `CapabilityToken` 或 `SignedBearerToken` 配置 |
| `WebsocketAuthPolicy` | 结构体 | 运行时认证策略 |
| `WebsocketAuthMode` | 枚举 | 运行时认证模式（含密钥/密文） |
| `WebsocketAuthError` | 结构体 | 认证失败错误（HTTP 状态码 + 消息） |
| `JwtClaims` | 结构体 | JWT 声明（exp、nbf、iss、aud） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `policy_from_settings` | `fn(settings) -> io::Result<WebsocketAuthPolicy>` | 从配置构建运行时策略 |
| `authorize_upgrade` | `fn(headers, policy) -> Result<(), WebsocketAuthError>` | 验证 WebSocket 升级请求的认证 |
| `should_warn_about_unauthenticated_non_loopback_listener` | `fn(bind_address, policy) -> bool` | 检查是否需要警告非环回地址无认证 |
| `verify_signed_bearer_token` | `fn(token, secret, issuer, audience, skew)` | 验证签名 JWT |

## 依赖关系

- **引用的 crate**: `jsonwebtoken`（JWT 解码/验证）、`sha2`（SHA-256）、`constant_time_eq`（常数时间比较）、`axum::http`、`clap`
- **被引用方**: `transport/websocket.rs`（升级时调用 `authorize_upgrade`）、`lib.rs`（构建策略）

## 实现备注

- Capability Token 模式在服务端只保存 token 的 SHA-256 哈希值，避免明文存储。
- JWT 验证手动实现声明校验（禁用了 `jsonwebtoken` 的默认验证），以支持自定义时钟偏移。
- 签名密钥最少 32 字节。
- 包含全面的单元测试覆盖篡改检测、有效令牌验证、多 audience、alg=none 拒绝、过期/缺失 exp 等。
