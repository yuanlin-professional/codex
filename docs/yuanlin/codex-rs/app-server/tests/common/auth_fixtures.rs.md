# `auth_fixtures.rs` — 认证测试固件工具

## 功能概述
提供用于测试的 ChatGPT 认证固件，包括 `ChatGptAuthFixture` 构建器（用于创建伪造的 ChatGPT 认证数据）、
`ChatGptIdTokenClaims` 结构体（用于构建 JWT ID Token 中的声明）、`encode_id_token` 函数（将声明编码为无签名 JWT 格式）
以及 `write_chatgpt_auth` 函数（将伪造认证写入测试目录的 auth.json 文件）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ChatGptAuthFixture` | struct | 测试用 ChatGPT 认证数据构建器，支持设置 access_token、refresh_token、account_id、claims 等字段 |
| `ChatGptIdTokenClaims` | struct | JWT ID Token 声明结构，包含 email、plan_type、chatgpt_user_id、chatgpt_account_id |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ChatGptAuthFixture::new` | `pub fn new(access_token: impl Into<String>) -> Self` | 创建新的认证固件，指定 access token |
| `encode_id_token` | `pub fn encode_id_token(claims: &ChatGptIdTokenClaims) -> Result<String>` | 将声明编码为 Base64 URL 安全的 JWT 字符串 |
| `write_chatgpt_auth` | `pub fn write_chatgpt_auth(codex_home: &Path, fixture: ChatGptAuthFixture, ...) -> Result<()>` | 将伪造认证数据写入 CODEX_HOME/auth.json |

## 依赖关系
- **引用的 crate**: `anyhow`, `base64`, `chrono`, `codex_app_server_protocol`, `codex_core::auth`, `codex_login`, `serde_json`
- **被引用方**: `common/lib.rs` 导出，广泛用于 auth、account 等测试模块

## 实现备注
JWT 使用 `alg: none` 算法生成，不进行真实签名验证，仅用于测试目的。
