# feedback

## 功能概述

`codex-feedback` 是 Codex 项目的反馈收集模块，负责在用户会话期间收集日志数据和结构化元数据，并在用户提交反馈时将这些信息上传到 Sentry 进行分析。它使用环形缓冲区（Ring Buffer）持续捕获全保真度的日志跟踪，确保在反馈提交时能提供完整的调试上下文。

## 架构说明

该模块的核心设计围绕以下组件：

1. **CodexFeedback** - 主要的反馈收集器，线程安全（基于 `Arc<FeedbackInner>`），生命周期贯穿整个会话。
2. **RingBuffer** - 固定容量（默认 4 MiB）的环形缓冲区，当缓冲区满时自动丢弃最早的数据，确保内存使用恒定。
3. **FeedbackMakeWriter / FeedbackWriter** - 实现了 `tracing_subscriber::fmt::writer::MakeWriter` trait，可直接集成到 tracing 日志管道中。
4. **FeedbackMetadataLayer** - 自定义 tracing Layer，拦截 target 为 `"feedback_tags"` 的日志事件，将其字段作为结构化标签（tags）收集。
5. **FeedbackSnapshot** - 某一时刻的反馈快照，包含日志字节、标签和诊断信息，可保存到临时文件或上传到 Sentry。

### 数据流

```
tracing 日志 -> FeedbackWriter -> RingBuffer (4 MiB)
                                          |
feedback_tags 事件 -> FeedbackMetadataLayer -> BTreeMap<String, String>
                                          |
            用户提交反馈 -> FeedbackSnapshot -> Sentry (带附件)
```

## 目录结构

```
feedback/
  Cargo.toml
  src/
    lib.rs                    # 核心实现：CodexFeedback、RingBuffer、Sentry 上传
    feedback_diagnostics.rs   # 反馈诊断信息收集（连接性诊断等）
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `anyhow` | 错误处理 |
| `codex-protocol` | ThreadId 类型定义 |
| `sentry` | 反馈数据上传到 Sentry |
| `tracing` / `tracing-subscriber` | 日志收集管道集成 |

## 核心接口/API

### CodexFeedback

```rust
impl CodexFeedback {
    pub fn new() -> Self;                                    // 创建默认 4 MiB 容量的收集器
    pub fn make_writer(&self) -> FeedbackMakeWriter;         // 获取 tracing 写入器
    pub fn logger_layer<S>(&self) -> impl Layer<S>;          // 获取全保真日志层（TRACE 级别）
    pub fn metadata_layer<S>(&self) -> impl Layer<S>;        // 获取元数据标签收集层
    pub fn snapshot(&self, session_id: Option<ThreadId>) -> FeedbackSnapshot;  // 创建快照
}
```

### FeedbackSnapshot

```rust
impl FeedbackSnapshot {
    pub fn save_to_temp_file(&self) -> io::Result<PathBuf>;  // 保存到临时文件
    pub fn upload_feedback(                                    // 上传到 Sentry
        &self,
        classification: &str,        // "bug" / "bad_result" / "good_result" / "safety_check"
        reason: Option<&str>,
        include_logs: bool,
        extra_attachment_paths: &[PathBuf],
        session_source: Option<SessionSource>,
        logs_override: Option<Vec<u8>>,
    ) -> Result<()>;
}
```

### 反馈分类

上传时的 `classification` 参数决定 Sentry 事件级别：
- `"bug"` / `"bad_result"` / `"safety_check"` -> Error 级别
- 其他 -> Info 级别
