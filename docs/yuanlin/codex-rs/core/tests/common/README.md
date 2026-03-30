# tests/common/ -- 测试公共库

## 功能概述

`common/` 是 Codex core 集成测试的公共辅助库，提供测试所需的各种基础设施：模拟 Responses API 服务器、SSE 流构建器、测试配置构建器、进程管理辅助、上下文快照辅助等。所有集成测试用例共享此库以保持测试代码的一致性和可维护性。

## 目录结构

```
common/
├── lib.rs                # 库入口，定义 test_config/test_codex_exec 等公共辅助函数
├── BUILD.bazel           # Bazel 构建文件
├── Cargo.toml            # Cargo 包定义
├── apps_test_server.rs   # 应用测试服务器（模拟 codex_apps MCP）
├── context_snapshot.rs   # 上下文快照辅助（用于快照测试）
├── process.rs            # 进程管理测试辅助
├── responses.rs          # Responses API 模拟服务器
├── streaming_sse.rs      # SSE 流构建辅助
├── test_codex.rs         # test_codex 辅助函数（Session 创建和管理）
├── test_codex_exec.rs    # test_codex_exec 辅助（codex exec 模式测试）
├── tracing.rs            # 测试追踪配置
└── zsh_fork.rs           # Zsh fork 测试辅助
```

## 依赖关系

- **上游依赖**：`codex-core`（被测库）、`anyhow`、`tempfile`、`regex-lite`、`codex-arg0`、`codex-utils-cargo-bin`
- **被依赖方**：`tests/suite/` 中的所有测试模块

## 核心接口/API

| 文件 | 说明 |
|---|---|
| `lib.rs` | 公共入口：`enable_deterministic_unified_exec_process_ids_for_tests()`、arg0 配置、insta 工作区配置 |
| `responses.rs` | Responses API 模拟服务器，支持 SSE 流响应 |
| `test_codex.rs` | Session 测试辅助函数 |
| `streaming_sse.rs` | SSE 事件流构建器 |
| `context_snapshot.rs` | 上下文快照比较辅助 |
