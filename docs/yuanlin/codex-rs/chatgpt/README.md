# chatgpt

## 功能概述

`codex-chatgpt` 是 Codex 项目中与 ChatGPT 后端 API 进行集成的 crate。它提供了以下核心能力:

- **ChatGPT API 客户端**: 封装了对 ChatGPT 后端的 HTTP GET 请求,支持 Bearer Token 认证和账户 ID 头部注入。
- **Connectors 管理**: 列出、缓存和合并 ChatGPT 应用连接器(App Connectors),支持目录连接器和工作区连接器的获取与合并。
- **任务获取与补丁应用**: 通过 `get_task` API 获取 Codex agent 任务的 diff 输出,并使用 `apply_command` 将 diff 应用到本地 Git 仓库。

## 架构说明

该 crate 采用模块化设计,各模块职责分明:

1. **chatgpt_client** -- 底层 HTTP 客户端,负责与 ChatGPT 后端 API 的通信。使用 `codex-login` 获取 token,通过 Bearer 认证发送请求。
2. **chatgpt_token** -- Token 管理模块,负责从认证系统初始化和获取 ChatGPT token 数据(包括 access_token 和 account_id)。
3. **connectors** -- 连接器管理模块,提供完整的连接器列表获取流程:
   - 从 ChatGPT 目录 API 分页获取公开连接器
   - 从工作区 API 获取企业连接器
   - 合并去重,标准化名称和描述
   - 与 MCP 工具的可访问连接器进行合并
   - 结合插件系统(PluginsManager)注入插件 App
4. **get_task** -- 任务查询模块,从 ChatGPT `/wham/tasks/{id}` 端点获取任务响应。
5. **apply_command** -- CLI 命令模块,提供 `codex apply <task_id>` 命令,将远端任务的 diff 应用到本地。

数据流: `CLI -> apply_command -> get_task -> chatgpt_client -> ChatGPT API`

## 目录结构

```
chatgpt/
  Cargo.toml
  src/
    lib.rs                 # 模块声明入口
    chatgpt_client.rs      # ChatGPT HTTP GET 请求封装
    chatgpt_token.rs       # Token 初始化与获取
    connectors.rs          # 连接器列表、缓存、合并逻辑
    get_task.rs            # 任务获取 API 及响应结构定义
    apply_command.rs       # apply 子命令的 CLI 定义与执行逻辑
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-core` | 核心配置 `Config` 类型、连接器相关工具函数 |
| `codex-login` | 认证管理、Token 数据结构 |
| `codex-connectors` | 连接器缓存和目录列表的底层抽象 |
| `codex-git-utils` | Git patch 应用 |
| `codex-utils-cli` | CLI 配置覆盖参数 |
| `codex-utils-cargo-bin` | Cargo 二进制工具 |
| `clap` | CLI 参数解析 |
| `serde` / `serde_json` | JSON 序列化/反序列化 |
| `tokio` | 异步运行时 |
| `anyhow` | 错误处理 |

## 核心接口/API

### `apply_command` 模块

```rust
/// CLI 子命令:应用远端 Codex agent 任务的最新 diff
pub struct ApplyCommand {
    pub task_id: String,
    pub config_overrides: CliConfigOverrides,
}

/// 执行 apply 命令
pub async fn run_apply_command(apply_cli: ApplyCommand, cwd: Option<PathBuf>) -> anyhow::Result<()>;

/// 从任务响应中提取并应用 diff
pub async fn apply_diff_from_task(task_response: GetTaskResponse, cwd: Option<PathBuf>) -> anyhow::Result<()>;
```

### `connectors` 模块

```rust
/// 获取完整的连接器列表(目录 + 可访问 + 插件合并)
pub async fn list_connectors(config: &Config) -> anyhow::Result<Vec<AppInfo>>;

/// 获取所有目录连接器(支持缓存)
pub async fn list_all_connectors(config: &Config) -> anyhow::Result<Vec<AppInfo>>;

/// 获取缓存中的所有目录连接器
pub async fn list_cached_all_connectors(config: &Config) -> Option<Vec<AppInfo>>;
```

### `get_task` 模块

```rust
/// 任务响应结构
pub struct GetTaskResponse {
    pub current_diff_task_turn: Option<AssistantTurn>,
}

/// PR 输出项,包含 diff 内容
pub struct PrOutputItem {
    pub output_diff: OutputDiff,
}
```
