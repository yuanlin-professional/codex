# `lib.rs` — codex-login crate 入口

## 功能概述
聚合并导出 codex-login crate 的所有公共 API。包括认证管理（`AuthManager`, `CodexAuth`, `AuthConfig`）、登录方法（`run_login_server`, `run_device_code_login`）、凭据操作（`login_with_api_key`, `logout`, `save_auth`）和 token 数据类型。

## 依赖关系
- **被引用方**: `codex-core`, `codex-chatgpt`, `cloud-tasks`, `analytics`
