# `external_editor.rs` — 外部编辑器集成

## 功能概述

本文件实现了外部编辑器的解析和启动功能，用于 TUI 的"用外部编辑器编辑"场景。它从 `VISUAL` 或 `EDITOR` 环境变量解析编辑器命令，将种子文本写入临时文件，启动编辑器进程等待其退出，然后读取并返回更新后的内容。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `EditorError` | 枚举 | 编辑器错误：MissingEditor / ParseFailed / EmptyCommand |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_editor_command` | `fn() -> Result<Vec<String>, EditorError>` | 从 VISUAL/EDITOR 环境变量解析编辑器命令 |
| `run_editor` | `async fn(seed: &str, editor_cmd: &[String]) -> Result<String>` | 将种子文本写入临时文件，启动编辑器并返回编辑后内容 |

## 依赖关系

- **引用的 crate**: `tempfile`（临时文件）、`tokio::process`（异步进程执行）、`shlex`/`winsplit`（命令解析）
- **被引用方**: `app.rs`（`LaunchExternalEditor` 事件处理）

## 实现备注

- 优先使用 `VISUAL`，其次 `EDITOR`。
- Windows 平台使用 `which` crate 解析 `.cmd` 等脚本垫片。
- 临时文件使用 `.md` 后缀，编辑器退出后立即读取内容。
- 包含 Unix 平台的集成测试，使用自定义 shell 脚本作为编辑器。
