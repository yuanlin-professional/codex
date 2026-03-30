# rollout

## 功能概述

`codex-rollout` 是 Codex 项目的会话持久化与发现系统 crate。它负责将 Codex 会话的完整历史记录(rollout)以 JSONL 格式写入磁盘,并提供会话列表查询、恢复、归档和 SQLite 状态数据库同步等能力。

核心职责包括:

- **会话录制**: 将每个会话的所有 `ResponseItem` 和事件以 JSONL 行格式追加写入文件。
- **会话发现**: 按日期目录结构扫描会话文件,支持分页、排序、过滤。
- **会话恢复**: 从 JSONL 文件加载历史记录,重建会话上下文。
- **会话归档**: 支持将会话移到归档目录。
- **SQLite 状态同步**: 将会话元数据写入 SQLite 数据库,加速列表查询和搜索。
- **会话命名**: 支持为会话分配和检索人类可读名称。
- **事件持久化策略**: 按 Limited/Extended 模式控制哪些事件类型应当持久化。

## 架构说明

1. **RolloutRecorder** (`recorder.rs`) -- 核心录制器:
   - 通过 `mpsc` 通道异步写入,避免阻塞调用者线程。
   - 支持延迟文件创建: 新会话先缓冲数据,直到显式调用 `persist()` 才创建文件。
   - 会话文件路径格式: `~/.codex/sessions/YYYY/MM/DD/rollout-YYYY-MM-DDThh-mm-ss-<uuid>.jsonl`
   - 支持 Create(新建)和 Resume(恢复)两种模式。
   - 自动收集 Git 信息(commit hash、branch、repo URL)写入会话元数据。

2. **EventPersistenceMode** (`policy.rs`) -- 事件持久化策略:
   - `Limited`: 仅持久化关键事件(用户消息、agent 消息、token 计数、上下文压缩等)。
   - `Extended`: 额外持久化执行结果、MCP 工具调用、web 搜索结果等。

3. **会话列表** (`list.rs`) -- 文件系统扫描:
   - 支持按创建时间或更新时间排序。
   - 支持按来源(CLI/VSCode/ChatGPT 等)和模型提供者过滤。
   - 支持 SQLite 数据库加速查询,带文件系统回退。

4. **SessionIndex** (`session_index.rs`) -- 会话命名索引,支持按 ID 查找名称和按名称查找路径。

5. **StateDbHandle** (`state_db.rs`) -- SQLite 状态数据库封装,支持读修复(read-repair)策略。

6. **Metadata** (`metadata.rs`) -- 从 JSONL 文件头部提取会话元数据。

7. **RolloutConfig** (`config.rs`) -- 配置视图,提供 codex_home、cwd、model_provider_id 等。

## 目录结构

```
rollout/
  Cargo.toml
  src/
    lib.rs                  # 模块声明,公开导出,常量定义
    config.rs               # RolloutConfig, RolloutConfigView trait
    recorder.rs             # RolloutRecorder 核心录制器
    policy.rs               # EventPersistenceMode 持久化策略
    list.rs                 # 会话文件扫描与分页
    metadata.rs             # 会话元数据提取
    session_index.rs        # 会话命名索引
    state_db.rs             # SQLite 状态数据库
    tests.rs                # 集成测试
    *_tests.rs              # 各模块单元测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-protocol` | `SessionSource`, `RolloutItem`, `SessionMeta` 等协议类型 |
| `codex-login` | 默认客户端和认证原始数据 |
| `codex-state` | `StateRuntime`, `ThreadMetadataBuilder`, SQLite 状态管理 |
| `codex-git-utils` | Git 信息收集 |
| `codex-file-search` | 文件搜索 |
| `codex-otel` | 遥测 |
| `codex-utils-path` / `codex-utils-string` | 路径比较和字符串截断 |
| `chrono` / `time` | 时间处理和格式化 |
| `tokio` | 异步 I/O 和通道 |
| `serde` / `serde_json` | JSONL 序列化/反序列化 |
| `uuid` | 会话 ID 生成 |
| `anyhow` | 错误处理 |
| `async-trait` | 异步 trait |
| `tracing` | 日志记录 |

## 核心接口/API

### RolloutRecorder

```rust
pub struct RolloutRecorder { /* tx, rollout_path, state_db, event_persistence_mode */ }

impl RolloutRecorder {
    /// 创建新的录制器(新建或恢复模式)
    pub async fn new(config: &impl RolloutConfigView, params: RolloutRecorderParams, ...) -> io::Result<Self>;

    /// 记录 rollout 项
    pub async fn record_items(&self, items: &[RolloutItem]) -> io::Result<()>;

    /// 物化文件并写入缓冲数据
    pub async fn persist(&self) -> io::Result<()>;

    /// 列出会话(带 SQLite 回退)
    pub async fn list_threads(config: &impl RolloutConfigView, page_size: usize, ...) -> io::Result<ThreadsPage>;

    /// 加载 JSONL 历史
    pub async fn load_rollout_items(path: &Path) -> io::Result<(Vec<RolloutItem>, Option<ThreadId>, usize)>;

    /// 获取用于恢复的完整历史
    pub async fn get_rollout_history(path: &Path) -> io::Result<InitialHistory>;
}
```

### 持久化策略

```rust
pub enum EventPersistenceMode {
    Limited,   // 仅关键事件
    Extended,  // 关键事件 + 执行详情
}
```
