# `cloud_requirements.rs` — 云端配置需求加载器

## 功能概述

提供异步加载云端配置需求（Cloud Requirements）的基础设施。`CloudRequirementsLoader` 封装一个共享的异步 Future，确保多次调用 `get()` 时底层请求只执行一次（通过 `futures::future::Shared`）。加载结果为 `Option<ConfigRequirementsToml>`，空值表示无云端需求。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CloudRequirementsLoadErrorCode` | 枚举 | 加载错误码：Auth / Timeout / Parse / RequestFailed / Internal |
| `CloudRequirementsLoadError` | 结构体 | 云端需求加载错误，包含错误码、消息和可选 HTTP 状态码 |
| `CloudRequirementsLoader` | 结构体 | 云端需求加载器，封装共享的异步 Future |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `CloudRequirementsLoader::new` | `<F: Future>(fut: F) -> Self` | 从异步 Future 创建加载器，自动转为 Shared Future |
| `CloudRequirementsLoader::get` | `async (&self) -> Result<Option<ConfigRequirementsToml>, Error>` | 获取云端需求，多次调用共享同一结果 |
| `CloudRequirementsLoadError::new` | `(code, status_code, message) -> Self` | 创建加载错误 |
| `CloudRequirementsLoadError::code` / `status_code` | getter 方法 | 获取错误码和 HTTP 状态码 |

## 依赖关系

- **引用的 crate**: `futures`、`thiserror`
- **被引用方**: `codex-config` 的 `lib.rs` 重导出；`codex-core` 的配置加载流程使用

## 实现备注

- `Default` 实现返回一个立即解析为 `Ok(None)` 的加载器，表示无云端需求。
- 通过 `Shared<BoxFuture>` 确保昂贵的网络请求仅执行一次，后续调用获取缓存的结果。
- 单元测试验证了共享 Future 只执行一次的语义。
