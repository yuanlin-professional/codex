# `remote.rs` -- 远程插件 API 客户端

## 功能概述
该文件实现了与 ChatGPT 后端 API 的远程插件交互功能，包括获取远程插件状态列表、获取推荐插件 ID 列表、以及执行插件启用/卸载变更操作。所有操作都通过 HTTP 请求与 `chatgpt_base_url` 通信，支持 Bearer Token 认证。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RemotePluginStatusSummary` | struct | 远程插件状态摘要，包含名称、市场名和启用状态 |
| `RemotePluginMutationResponse` | struct (private) | 插件变更操作的响应，包含 ID 和启用状态 |
| `RemotePluginFetchError` | enum | 远程插件获取错误，覆盖认证、请求、响应状态、解析等错误 |
| `RemotePluginMutationError` | enum | 远程插件变更错误，额外包含无效基础 URL、意外的插件 ID/状态等错误 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `fetch_remote_plugin_status` | `async fn(config, auth) -> Result<Vec<RemotePluginStatusSummary>>` | 获取远程已安装插件的状态列表（GET /plugins/list） |
| `fetch_remote_featured_plugin_ids` | `async fn(config, auth, product) -> Result<Vec<String>>` | 获取推荐插件 ID 列表（GET /plugins/featured），支持按产品过滤 |
| `enable_remote_plugin` | `async fn(config, auth, plugin_id) -> Result<()>` | 启用远程插件（POST /plugins/{id}/enable） |
| `uninstall_remote_plugin` | `async fn(config, auth, plugin_id) -> Result<()>` | 卸载远程插件（POST /plugins/{id}/uninstall） |
| `post_remote_plugin_mutation` | `async fn(config, auth, plugin_id, action) -> Result<RemotePluginMutationResponse>` | 通用的远程插件变更请求 |

## 依赖关系
- **引用的 crate**: `reqwest`, `serde`, `url`, `codex_protocol`
- **引用的内部模块**: `crate::auth::CodexAuth`, `crate::config::Config`, `crate::default_client::build_reqwest_client`
- **被引用方**: `manager.rs` 调用获取和变更函数；`mod.rs` 导出 `RemotePluginFetchError` 和 `fetch_remote_featured_plugin_ids`

## 实现备注
- 仅支持 ChatGPT 认证模式，API Key 认证不受支持
- 获取请求超时为 30 秒（状态列表）和 10 秒（推荐列表）
- 变更请求超时为 30 秒
- 推荐插件接口支持按产品过滤（通过 `platform` 查询参数），默认为 Codex
- 变更操作会验证响应中的插件 ID 和启用状态是否符合预期
- 远程市场名默认为 "openai-curated"
