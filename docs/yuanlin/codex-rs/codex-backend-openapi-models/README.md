# codex-backend-openapi-models

## 功能概述

`codex-backend-openapi-models` 是从 Codex 后端 OpenAPI 规范自动生成的 Rust 数据模型库。它提供了与 Codex 后端 REST API 通信所需的所有请求/响应结构体，作为 `backend-client` crate 的底层类型基础。

该 crate 不包含手写代码，所有类型由 OpenAPI 代码生成器产出。当前采用精选导出模式，仅导出工作空间中实际使用的类型子集。

涵盖的模型领域：

- **配置管理**：`ConfigFileResponse` -- 后端托管的配置文件响应。
- **云端任务**：`CodeTaskDetailsResponse`、`TaskResponse`、`TaskListItem`、`PaginatedListTaskListItem` -- 任务详情、列表和分页。
- **Git/PR 集成**：`GitPullRequest`、`ExternalPullRequestResponse` -- Pull Request 相关模型。
- **速率限制**：`RateLimitStatusPayload`、`RateLimitStatusDetails`、`RateLimitWindowSnapshot`、`AdditionalRateLimitDetails`、`CreditStatusDetails` -- 完整的速率限制和额度模型。
- **计划类型**：`PlanType` 枚举 -- 包含 Free、Plus、Pro、Team、Enterprise 等所有账户计划类型。

## 架构说明

该 crate 是纯数据模型层，不包含业务逻辑：

```
OpenAPI 规范 (.yaml/.json)
        |
        v
    代码生成器 (regen script)
        |
        v
    src/models/*.rs  (自动生成的 Rust 结构体)
        |
        v
    src/models/mod.rs (模块导出)
        |
        v
    src/lib.rs (crate 级别导出)
```

所有生成的模型使用 `serde` 进行序列化/反序列化，部分使用 `serde_with` 进行高级序列化控制。由于自动生成的代码可能包含 `unwrap` / `expect` 调用，crate 级别通过 `#![allow(clippy::unwrap_used, clippy::expect_used)]` 放宽了对应的 lint 规则。

## 目录结构

```
codex-backend-openapi-models/
├── Cargo.toml
└── src/
    ├── lib.rs                              # crate 入口，lint 放宽，re-export models 模块
    └── models/
        ├── mod.rs                          # 精选导出列表
        ├── config_file_response.rs         # 配置文件响应
        ├── code_task_details_response.rs   # 任务详情响应
        ├── task_response.rs                # 任务响应
        ├── task_list_item.rs               # 任务列表项
        ├── paginated_list_task_list_item_.rs # 分页任务列表
        ├── external_pull_request_response.rs # 外部 PR 响应
        ├── git_pull_request.rs             # Git PR 模型
        ├── rate_limit_status_payload.rs    # 速率限制载荷（含 PlanType 枚举）
        ├── rate_limit_status_details.rs    # 速率限制详情
        ├── rate_limit_window_snapshot.rs   # 速率限制窗口快照
        ├── additional_rate_limit_details.rs # 额外速率限制详情
        └── credit_status_details.rs        # 额度详情
```

## 依赖关系

| 依赖 | 说明 |
|------|------|
| `serde` | 序列化/反序列化（derive 特性） |
| `serde_json` | JSON 处理 |
| `serde_with` | 高级序列化辅助 |

该 crate 依赖极少，仅包含序列化相关的基础依赖，确保作为纯数据模型层的轻量性。

## 核心接口/API

### 配置

```rust
pub struct ConfigFileResponse { /* 后端配置文件响应 */ }
```

### 任务模型

```rust
pub struct CodeTaskDetailsResponse { /* 任务详情 */ }
pub struct TaskResponse { /* 任务响应 */ }
pub struct TaskListItem { /* 任务列表项 */ }
pub struct PaginatedListTaskListItem { /* 分页任务列表 */ }
```

### Git/PR 模型

```rust
pub struct ExternalPullRequestResponse { /* 外部 PR 响应 */ }
pub struct GitPullRequest { /* Git PR */ }
```

### 速率限制模型

```rust
pub struct RateLimitStatusPayload {
    pub plan_type: PlanType,
    pub rate_limit: Option<Option<Box<RateLimitStatusDetails>>>,
    pub additional_rate_limits: Option<Option<Vec<AdditionalRateLimitDetails>>>,
    pub credits: Option<Option<Box<CreditStatusDetails>>>,
}

pub enum PlanType {
    Free, Go, Plus, Pro, Team, Business, Enterprise,
    SelfServeBusinessUsageBased, EnterpriseCbpUsageBased,
    Edu, Education, Guest, FreeWorkspace, Quorum, K12,
}

pub struct RateLimitStatusDetails { /* 速率限制详情 */ }
pub struct RateLimitWindowSnapshot { /* 窗口快照 */ }
pub struct AdditionalRateLimitDetails { /* 额外限制 */ }
pub struct CreditStatusDetails { /* 额度详情 */ }
```
