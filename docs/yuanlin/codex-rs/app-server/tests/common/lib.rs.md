# `lib.rs` — 测试公共模块入口

## 功能概述
作为 `common/` 测试辅助模块的入口文件，聚合并重新导出所有子模块的公共 API。
包含子模块 `analytics_server`、`auth_fixtures`、`config`、`mcp_process`、`mock_model_server`、
`models_cache`、`responses` 和 `rollout`。此外还提供 `to_response` 泛型辅助函数，
用于将 JSON-RPC 响应反序列化为具体的响应类型。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `to_response<T>` | `pub fn to_response<T: DeserializeOwned>(response: JSONRPCResponse) -> anyhow::Result<T>` | 将 JSONRPCResponse 的 result 字段反序列化为目标类型 T |

## 依赖关系
- **引用的 crate**: `codex_app_server_protocol`, `core_test_support`, `serde`, `serde_json`
- **被引用方**: 几乎所有测试文件通过 `app_test_support` 引用此模块
