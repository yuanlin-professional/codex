# `engine.rs` (mod.rs) -- Hook 引擎核心，ClaudeHooksEngine 定义与各事件的入口

## 功能概述

本文件是 `engine` 模块的根文件，定义了 `ClaudeHooksEngine` 这一核心引擎结构体以及 `CommandShell` 和 `ConfiguredHandler` 两个基础类型。`ClaudeHooksEngine` 封装了所有 Hook 处理器的生命周期管理，提供了各种事件（SessionStart、PreToolUse、PostToolUse、UserPromptSubmit、Stop）的预览和执行入口方法。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CommandShell` | struct | Shell 配置，包含 program 和 args（空 program 使用默认 shell） |
| `ConfiguredHandler` | struct | 已配置的 Hook 处理器实例，含事件名、matcher、command、timeout、来源路径和显示顺序 |
| `ClaudeHooksEngine` | struct | Hook 引擎核心，持有所有 handlers、启动警告和 shell 配置 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ConfiguredHandler::run_id` | `fn(&self) -> String` | 生成格式为 `{event_label}:{display_order}:{source_path}` 的唯一运行标识 |
| `ClaudeHooksEngine::new` | `fn(enabled, config_layer_stack, shell) -> Self` | 构造引擎，若禁用或 Windows 则返回空引擎，否则执行 handler 发现 |
| `preview_*` / `run_*` | 各事件的预览和执行方法 | 委托给对应的 `events` 子模块 |
| `warnings` | `fn(&self) -> &[String]` | 返回初始化过程中收集到的警告信息 |

## 依赖关系

- **引用的 crate**: `codex_config`（ConfigLayerStack）、`codex_protocol`（HookRunSummary）
- **引用的内部模块**: 所有 `events` 子模块的 Request/Outcome 类型、`engine::discovery`、`engine::schema_loader`
- **被引用方**: `codex-core` 或上层 crate 通过 `ClaudeHooksEngine` 使用 Hook 系统

## 实现备注

- Windows 下 Hook 系统整体禁用，返回一条警告信息。
- 构造时会调用 `schema_loader::generated_hook_schemas()` 来预加载并校验 JSON Schema（结果被忽略，仅触发加载）。
- 所有子模块声明为 `pub(crate)`，仅在 crate 内部可见。
