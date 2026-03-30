# `slash_command.rs` — 斜杠命令定义

## 功能概述

定义所有 TUI 支持的斜杠命令（`/model`、`/fast`、`/resume`、`/diff` 等）。
每个命令关联有描述、是否支持内联参数、是否可在任务执行中使用、以及平台可见性。
枚举顺序决定弹出菜单中的显示顺序，常用命令排在前面。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SlashCommand` | enum | 所有斜杠命令的枚举（约 40 个变体） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `description` | `(self) -> &str` | 命令描述文本 |
| `command` | `(self) -> &str` | 不含 `/` 的命令字符串 |
| `supports_inline_args` | `(self) -> bool` | 是否支持行内参数 |
| `available_during_task` | `(self) -> bool` | 任务执行中是否可用 |
| `built_in_slash_commands` | `() -> Vec<(&str, SlashCommand)>` | 获取所有可见的命令列表 |

## 依赖关系

- **引用的 crate**: `strum`（EnumIter、EnumString、AsRefStr 派生宏）

## 实现备注

- 使用 `strum` 的 `serialize_all = "kebab-case"` 自动生成命令字符串。
- 部分命令有别名（如 `clean` -> `Stop`）。
- 调试命令（`MemoryDrop`/`MemoryUpdate`）在描述中标记为 "DO NOT USE"。
- 平台可见性：`SandboxReadRoot` 仅 Windows，`Copy` 非 Android，调试命令仅 debug 构建。
