# `manager.rs` -- 模型管理器核心实现

## 功能概述

`ModelsManager` 是模型发现和元数据管理的核心组件，协调远程模型列表的获取、磁盘缓存、内存中的模型目录维护以及模型信息查找。支持三种刷新策略（Online/Offline/OnlineIfUncached），通过 ETag 增量更新机制减少不必要的网络请求。还负责根据认证模式（ChatGPT vs API Key）过滤可见模型，以及提供协作模式预设列表。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ModelsManager` | 结构体 | 模型管理器，持有远程模型列表（RwLock 保护）、缓存管理器、认证管理器等 |
| `RefreshStrategy` | 枚举 | 模型刷新策略：Online（始终网络获取）、Offline（仅缓存）、OnlineIfUncached（缓存优先） |
| `CatalogMode` | 枚举 | 目录模式：Default（从内置 models.json 启动）、Custom（使用调用方提供的目录） |
| `ModelsRequestTelemetry` | 结构体 | /models 请求的遥测数据收集器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ModelsManager::new` | `fn(PathBuf, Arc<AuthManager>, Option<ModelsResponse>, CollaborationModesConfig) -> Self` | 构建模型管理器，初始化内置目录或自定义目录 |
| `list_models` | `async fn(&self, RefreshStrategy) -> Vec<ModelPreset>` | 刷新并返回可用模型预设列表（按优先级排序、按认证过滤） |
| `list_collaboration_modes` | `fn(&self) -> Vec<CollaborationModeMask>` | 返回协作模式预设列表 |
| `get_default_model` | `async fn(&self, &Option<String>, RefreshStrategy) -> String` | 获取默认模型标识符 |
| `get_model_info` | `async fn(&self, &str, &Config) -> ModelInfo` | 查找模型元数据，应用远程覆盖和配置调整 |
| `find_model_by_longest_prefix` | `fn(&str, &[ModelInfo]) -> Option<ModelInfo>` | 最长前缀匹配模型 slug |
| `find_model_by_namespaced_suffix` | `fn(&str, &[ModelInfo]) -> Option<ModelInfo>` | 支持 `namespace/model-name` 格式的命名空间匹配 |
| `refresh_if_new_etag` | `async fn(&self, String)` | ETag 变化时触发刷新，相同时仅续期缓存 |
| `refresh_available_models` | `async fn(&self, RefreshStrategy) -> CoreResult<()>` | 根据策略执行模型刷新 |
| `fetch_and_update_models` | `async fn(&self) -> CoreResult<()>` | 从远程 API 获取模型列表并更新缓存 |
| `apply_remote_models` | `async fn(&self, Vec<ModelInfo>)` | 合并远程模型到内置目录（按 slug 去重更新） |
| `build_available_models` | `fn(&self, Vec<ModelInfo>) -> Vec<ModelPreset>` | 排序、过滤并标记默认模型 |

## 依赖关系

- **引用的 crate**: `codex_api`、`codex_otel`、`codex_protocol`、`http`、`tokio`
- **被引用方**: 整个 codex 核心模块在需要模型信息时使用此管理器

## 实现备注

- 远程模型列表存储在 `RwLock<Vec<ModelInfo>>` 中，支持并发读取和独占写入。
- Custom 目录模式下不会执行任何远程刷新，确保调用方提供的目录不被覆盖。
- 非 ChatGPT 认证模式下仅使用缓存，不发起网络请求。
- 模型匹配采用最长前缀匹配策略，确保 `gpt-5.3-codex` 优先匹配 `gpt-5.3-codex` 而非 `gpt-5.3`。
- 命名空间匹配仅支持单层（`namespace/model`），拒绝多层路径，且命名空间必须为 ASCII 字母数字或下划线。
- 远程获取超时为 5 秒，防止网络问题阻塞启动流程。
- 遥测数据同时写入 `log_only` 和 `trace_safe` 两个 target，分别用于日志和追踪。
