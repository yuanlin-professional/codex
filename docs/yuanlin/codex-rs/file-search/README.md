# file-search

## 功能概述

`codex-file-search` 是 Codex 项目的文件搜索模块，提供高性能的模糊文件名搜索功能。它结合了并行文件系统遍历（基于 `ignore` crate）和增量模糊匹配引擎（基于 `nucleo`），支持交互式搜索会话（实时查询更新）和一次性搜索两种模式。搜索结果按相关性评分排序，可选返回匹配字符位置用于高亮显示。

## 架构说明

该模块采用双线程（Worker）架构：

### 1. Walker Worker（文件遍历线程）
- 使用 `ignore::WalkBuilder` 并行遍历文件系统
- 尊重 `.gitignore` 规则（通过 `require_git(true)` 确保仅在 Git 仓库内生效）
- 将发现的文件路径通过 `Injector` 注入到 nucleo 匹配引擎
- 支持自定义排除模式和符号链接跟随
- 定期检查取消标志（每 1024 个文件）

### 2. Matcher Worker（匹配线程）
- 使用 `nucleo::Nucleo` 增量模糊匹配引擎
- 监听查询更新和 nucleo 通知信号
- 去抖动（debounce）机制减少不必要的更新
- 通过 `SessionReporter` trait 回调通知匹配结果

### 通信机制

```
Walker Worker  --Injector-->  Nucleo Engine  --notify-->  Matcher Worker
                                                              |
FileSearchSession.update_query --WorkSignal-->  Matcher Worker
                                                              |
                                               SessionReporter.on_update/on_complete
```

### 搜索会话

`FileSearchSession` 支持生命周期内多次查询更新，无需重新遍历文件系统。查询变更时 nucleo 进行增量匹配（`append` 优化），支持实时搜索场景。

## 目录结构

```
file-search/
  Cargo.toml
  src/
    lib.rs    # 核心实现：搜索引擎、Worker、Session、结果类型
    cli.rs    # CLI 参数定义
    main.rs   # 独立可执行文件入口
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `nucleo` | 高性能增量模糊匹配引擎 |
| `ignore` | 并行文件遍历（尊重 .gitignore） |
| `crossbeam-channel` | 多生产者通道（Worker 间通信） |
| `clap` | CLI 参数解析 |
| `serde` / `serde_json` | 搜索结果序列化 |
| `tokio` | 异步运行时（CLI 模式） |
| `anyhow` | 错误处理 |

## 核心接口/API

### 搜索函数

```rust
// 一次性搜索（阻塞）
pub fn run(
    pattern_text: &str,
    roots: Vec<PathBuf>,
    options: FileSearchOptions,
    cancel_flag: Option<Arc<AtomicBool>>,
) -> anyhow::Result<FileSearchResults>;

// 创建交互式搜索会话
pub fn create_session(
    search_directories: Vec<PathBuf>,
    options: FileSearchOptions,
    reporter: Arc<dyn SessionReporter>,
    cancel_flag: Option<Arc<AtomicBool>>,
) -> anyhow::Result<FileSearchSession>;
```

### 搜索会话

```rust
impl FileSearchSession {
    pub fn update_query(&self, pattern_text: &str);  // 更新查询（增量匹配）
}
// Drop 时自动停止搜索
```

### 类型定义

- **`FileMatch`** - 单条匹配结果（score、path、match_type、root、indices）
- **`MatchType`** - 匹配类型（`File` / `Directory`）
- **`FileSearchResults`** - 搜索结果（matches + total_match_count）
- **`FileSearchSnapshot`** - 搜索快照（含 query、scanned_file_count、walk_complete 状态）
- **`FileSearchOptions`** - 搜索选项（limit、exclude、threads、compute_indices、respect_gitignore）

### SessionReporter trait

```rust
pub trait SessionReporter: Send + Sync + 'static {
    fn on_update(&self, snapshot: &FileSearchSnapshot);  // 匹配结果变更
    fn on_complete(&self);                                // 搜索完成或取消
}
```

### 排序工具

- `cmp_by_score_desc_then_path_asc(score_of, path_of)` - 通用比较器：先按评分降序，再按路径升序。
- `file_name_from_path(path: &str) -> String` - 提取文件名。
