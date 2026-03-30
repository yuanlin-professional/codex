# cloud-tasks-client

## 功能概述

`codex-cloud-tasks-client` 是 Codex 项目的云任务后端客户端库，为 `codex-cloud-tasks` 提供与云端任务 API 通信的抽象接口。它定义了统一的 `CloudBackend` trait，并提供两种实现：`HttpClient`（真实 HTTP 后端）和 `MockClient`（内存模拟后端），支持任务的创建、查询、列举和差异比较等操作。

## 架构说明

该 crate 采用 trait 抽象 + feature flag 的设计模式：

### CloudBackend trait
定义了与云端任务系统交互的所有操作接口，由具体实现类来完成。

### Feature Flags

| Feature | 说明 |
|---------|------|
| `online`（默认启用） | 启用 `HttpClient` 实现，依赖 `codex-backend-client` |
| `mock` | 启用 `MockClient` 实现，内存模拟 |

### 实现类

1. **HttpClient** - 通过 `codex-backend-client` 与真实后端 API 通信的 HTTP 客户端。
2. **MockClient** - 纯内存实现，用于测试和开发环境。

### 数据模型

任务生命周期：`Created -> Running -> Completed/Failed`

```
创建任务 -> CreatedTask (含 TaskId)
查询状态 -> TaskStatus (含 attempts: Vec<TurnAttempt>)
列举任务 -> TaskListPage (含 TaskSummary 列表)
获取差异 -> DiffSummary
应用结果 -> ApplyOutcome / ApplyStatus
```

## 目录结构

```
cloud-tasks-client/
  Cargo.toml
  src/
    lib.rs    # 模块入口，导出 trait 和类型
    api.rs    # CloudBackend trait 定义和核心数据类型
    http.rs   # HttpClient 实现（feature = "online"）
    mock.rs   # MockClient 实现（feature = "mock"）
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `anyhow` | 错误处理 |
| `async-trait` | 异步 trait 定义 |
| `chrono` | 时间类型（含 serde 支持） |
| `diffy` | diff 生成 |
| `serde` / `serde_json` | 数据序列化 |
| `thiserror` | 错误类型派生 |
| `codex-backend-client`（可选） | 真实后端 HTTP 客户端（feature = "online"） |
| `codex-git-utils` | Git 工具 |

## 核心接口/API

### CloudBackend trait

```rust
#[async_trait]
pub trait CloudBackend: Send + Sync {
    // 任务操作（由具体实现定义）
}
```

### 类型定义

- **`TaskId`** - 任务标识符
- **`CreatedTask`** - 新建任务信息
- **`TaskStatus`** - 任务状态详情
- **`TaskSummary`** - 任务摘要
- **`TaskListPage`** - 任务列表页
- **`TaskText`** - 任务文本内容
- **`TurnAttempt`** - 轮次尝试记录
- **`AttemptStatus`** - 尝试状态
- **`DiffSummary`** - 差异摘要
- **`ApplyOutcome`** / **`ApplyStatus`** - 应用结果和状态
- **`CloudTaskError`** - 云任务错误类型
- **`Result<T>`** - 类型别名 `Result<T, CloudTaskError>`

### 客户端实现

- **`HttpClient`**（feature = "online"）- 真实 HTTP 后端客户端
- **`MockClient`**（feature = "mock"）- 内存模拟客户端
