# connectors

## 功能概述

`codex-connectors` 是 Codex 项目中模型连接器(App Connectors)的底层抽象层 crate。它提供了与具体 AI 提供商无关的连接器目录列表获取、缓存、合并和标准化逻辑。上层 crate(如 `codex-chatgpt`)通过注入 HTTP 请求函数来使用此抽象层。

核心职责包括:

- **连接器目录分页获取**: 支持分页遍历连接器目录 API,自动处理 `nextToken` 分页。
- **全局缓存**: 使用带 TTL(1 小时)的进程级缓存,避免重复请求。
- **工作区连接器合并**: 对个人连接器和工作区连接器进行去重合并,保留最完整的字段。
- **名称和 URL 标准化**: 统一连接器名称、描述、安装链接的格式。

## 架构说明

整个 crate 仅有一个 `lib.rs` 文件,采用函数式设计,核心组件包括:

1. **缓存层**:
   - `AllConnectorsCacheKey`: 缓存键,包含 base URL、account ID、user ID、是否工作区账户。
   - `ALL_CONNECTORS_CACHE`: `LazyLock<StdMutex<Option<...>>>` 全局静态缓存。
   - `cached_all_connectors()`: 查询缓存。
   - 缓存 TTL 为 `CONNECTORS_CACHE_TTL`(3600 秒)。

2. **列表获取**:
   - `list_all_connectors_with_options()`: 泛型高层函数,接受一个 `fetch_page` 闭包来执行实际 HTTP 请求。先检查缓存,命中则直接返回;未命中则分别获取目录连接器和工作区连接器。
   - `list_directory_connectors()`: 分页获取 `/connectors/directory/list` 端点。
   - `list_workspace_connectors()`: 获取 `/connectors/directory/list_workspace` 端点。

3. **合并与标准化**:
   - `merge_directory_apps()`: 按 ID 去重合并,保留最完整的字段集。
   - `normalize_connector_name()` / `normalize_connector_value()`: 清理空白。
   - `connector_install_url()` / `connector_name_slug()`: 生成标准安装 URL。

4. **数据类型**:
   - `DirectoryListResponse`: API 分页响应。
   - `DirectoryApp`: 连接器目录项,包含 id、name、description、branding、metadata 等。
   - 最终转换为 `AppInfo`(来自 `codex-app-server-protocol`)。

## 目录结构

```
connectors/
  Cargo.toml
  src/
    lib.rs    # 全部实现:缓存、列表获取、合并、标准化、测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-app-server-protocol` | `AppInfo`、`AppBranding`、`AppMetadata` 类型定义 |
| `serde` | 反序列化 API 响应 |
| `urlencoding` | 分页 token 的 URL 编码 |
| `anyhow` | 错误处理 |

## 核心接口/API

### 缓存

```rust
/// 缓存 TTL: 1 小时
pub const CONNECTORS_CACHE_TTL: Duration = Duration::from_secs(3600);

/// 缓存键
pub struct AllConnectorsCacheKey { /* chatgpt_base_url, account_id, ... */ }

/// 查询缓存中的连接器列表
pub fn cached_all_connectors(cache_key: &AllConnectorsCacheKey) -> Option<Vec<AppInfo>>;
```

### 列表获取

```rust
/// 获取所有连接器(带缓存和分页),通过 fetch_page 闭包注入 HTTP 请求
pub async fn list_all_connectors_with_options<F, Fut>(
    cache_key: AllConnectorsCacheKey,
    is_workspace_account: bool,
    force_refetch: bool,
    fetch_page: F,
) -> anyhow::Result<Vec<AppInfo>>
where
    F: FnMut(String) -> Fut,
    Fut: Future<Output = anyhow::Result<DirectoryListResponse>>;
```

### 数据类型

```rust
/// API 分页响应
pub struct DirectoryListResponse {
    apps: Vec<DirectoryApp>,
    next_token: Option<String>,
}

/// 连接器目录项
pub struct DirectoryApp {
    id: String,
    name: String,
    description: Option<String>,
    branding: Option<AppBranding>,
    // ... 其他字段
}
```
