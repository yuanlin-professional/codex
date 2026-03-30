# `lib.rs` -- codex-hooks crate 根模块

## 功能概述

该文件是 `codex-hooks` crate 的根模块，负责声明子模块结构并重导出公共 API。它将内部模块（engine、events、legacy_notify、registry、schema、types）的关键类型和函数汇总导出，使外部使用者通过 `codex_hooks::*` 即可访问所有钩子系统的公共接口。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| - | - | 本文件不定义新类型，仅重导出 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| - | - | 本文件仅执行 `pub use` 重导出 |

## 依赖关系

- **引用的 crate**: 内部子模块 `engine`, `events`, `legacy_notify`, `registry`, `schema`, `types`
- **被引用方**: 所有依赖 `codex-hooks` crate 的外部模块

## 实现备注

重导出的主要类型和函数包括：
- 事件相关：`PostToolUseOutcome/Request`, `PreToolUseOutcome/Request`, `SessionStartOutcome/Request/Source`, `StopOutcome/Request`, `UserPromptSubmitOutcome/Request`
- 核心类型：`Hook`, `HookEvent`, `HookEventAfterAgent`, `HookEventAfterToolUse`, `HookPayload`, `HookResponse`, `HookResult`, `HookToolInput`, `HookToolInputLocalShell`, `HookToolKind`
- 注册表：`Hooks`, `HooksConfig`, `command_from_argv`
- 工具函数：`legacy_notify_json`, `notify_hook`, `write_schema_fixtures`
