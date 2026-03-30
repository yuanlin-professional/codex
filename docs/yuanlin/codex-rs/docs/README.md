# codex-rs/docs -- Rust 工作空间文档

## 功能概述

`codex-rs/docs/` 目录包含 Codex Rust 工作空间的核心技术文档，涵盖 Bazel 构建指南、MCP 服务器接口规范和内部通信协议规范。这些文档面向 Codex 的开发者和集成者。

## 目录结构

```
codex-rs/docs/
├── bazel.md                  # Bazel 构建指南
├── codex_mcp_interface.md    # MCP 服务器接口文档（实验性）
└── protocol_v1.md            # 内部通信协议 v1 规范
```

## 文档说明

### bazel.md -- Bazel 构建指南
- **内容**：介绍 codex-rs 的 Bazel 构建架构
- **核心概念**：
  - `MODULE.bazel` 定义依赖和 Rust 工具链
  - `rules_rs` 从 Cargo.toml/Cargo.lock 导入第三方 crate
  - `defs.bzl` 提供 `codex_rust_crate` 宏，统一封装 rust_library/rust_binary/rust_test
  - 各 crate 的 `BUILD.bazel` 使用 `codex_rust_crate` 并按需调整
- **操作指南**：依赖更新后运行 `just bazel-lock-update`，本地验证用 `just bazel-lock-check`

### codex_mcp_interface.md -- MCP 服务器接口（实验性）
- **状态**：实验性，可能随时变更
- **服务器入口**：`codex mcp-server`
- **传输方式**：MCP over stdio（JSON-RPC 2.0，行分隔）
- **核心 RPC**：
  - 线程管理：`thread/start`、`thread/resume`、`thread/fork`、`thread/read`、`thread/list`
  - Turn 控制：`turn/start`、`turn/steer`、`turn/interrupt`
  - 账号管理：`account/read`、`account/login/start`、`account/logout`
  - 配置管理：`config/read`、`config/value/write`
  - 模型与应用：`model/list`、`app/list`、`collaborationMode/list`
- **审批机制**：服务器向客户端发送 `applyPatchApproval` / `execCommandApproval` 请求，客户端回复 `allow` 或 `deny`

### protocol_v1.md -- 内部通信协议 v1
- **核心实体**：
  - `Model`：Responses REST API
  - `Codex`：核心引擎，通过 SQ/EQ 队列对与 UI 通信
  - `Session`：当前配置和状态
  - `Task`：响应用户输入的执行单元
  - `Turn`：Task 中的一个迭代周期（请求模型 -> 收集响应 -> 执行命令/应用补丁）
- **关键消息类型**：
  - `Op`（UI -> Codex）：`UserTurn`、`Interrupt`、`ExecApproval`、`ListSkills`
  - `EventMsg`（Codex -> UI）：`AgentMessage`、`TurnStarted`、`TurnComplete`、`ExecApprovalRequest`、`Error`
- **传输方式**：支持跨线程通道、IPC、stdin/stdout、TCP、HTTP2、gRPC

## 依赖关系

- **协议定义源码**：`protocol/src/protocol.rs`、`core/src/agent.rs`、`app-server-protocol/src/protocol/`
- **参考实现**：`app-server/`（MCP 服务器实现）
