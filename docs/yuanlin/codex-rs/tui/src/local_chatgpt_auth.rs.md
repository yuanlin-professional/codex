# `local_chatgpt_auth.rs` — 本地 ChatGPT 认证加载

## 功能概述

本文件实现了从本地存储加载 ChatGPT 认证信息的功能。它读取 `auth.json` 文件，验证是否为 ChatGPT 登录（而非 API Key），提取 access_token、account_id 和 plan_type，并支持强制 workspace ID 匹配验证。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LocalChatgptAuth` | 结构体 | 本地 ChatGPT 认证信息：access_token、chatgpt_account_id、chatgpt_plan_type |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_local_chatgpt_auth` | `fn(codex_home, auth_credentials_store_mode, forced_workspace_id) -> Result<LocalChatgptAuth, String>` | 加载并验证本地 ChatGPT 认证 |

## 依赖关系

- **引用的 crate**: `codex_core::auth`（认证文件读写）、`codex_app_server_protocol::AuthMode`
- **被引用方**: 需要本地 ChatGPT 认证的连接器功能

## 实现备注

- 明确拒绝 API Key 类型的认证（`auth_mode == ApiKey` 或存在 `openai_api_key`）。
- plan_type 从 JWT id_token 中提取并转为小写。
- 当指定 `forced_chatgpt_workspace_id` 时，验证 account_id 必须匹配。
- 包含完整的单元测试，使用伪造的 JWT token 验证各种场景。
