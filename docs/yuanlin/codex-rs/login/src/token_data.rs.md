# `token_data.rs` — JWT Token 数据解析

## 功能概述
定义 ChatGPT 认证 token 的数据结构并实现 JWT 解析逻辑。从 ID token 的 JWT payload 中提取 email、plan type、user ID 和 account ID 等声明信息。支持多种订阅计划类型（Free/Go/Plus/Pro/Team/Business/Enterprise/Edu）的识别和序列化。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `TokenData` | 结构体 | 完整 token 数据：id_token, access_token, refresh_token, account_id |
| `IdTokenInfo` | 结构体 | JWT 中提取的信息：email, plan_type, user_id, account_id, raw_jwt |
| `PlanType` | 枚举 | Known(KnownPlan) 或 Unknown(String) |
| `KnownPlan` | 枚举 | Free/Go/Plus/Pro/Team/Business/Enterprise/Edu 等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_chatgpt_jwt_claims` | `fn(jwt) -> Result<IdTokenInfo>` | 解析 JWT 并提取 ChatGPT 相关声明 |
| `parse_jwt_expiration` | `fn(jwt) -> Result<Option<DateTime>>` | 解析 JWT 过期时间 |

## 依赖关系
- **引用的 crate**: `base64`, `serde`, `chrono`, `thiserror`
- **被引用方**: `manager.rs`, `storage.rs`, `server.rs`
