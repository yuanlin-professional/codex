# `model_migration.rs` — 模型迁移提示界面

## 功能概述

在 TUI 启动时，当检测到推荐的新模型版本时，显示一个全屏模型迁移提示界面。
支持接受迁移、拒绝迁移或退出三种操作结果。提示可以使用预定义文案或自定义
Markdown 模板渲染内容。界面在备用屏幕（alternate screen）中渲染以避免污染滚动历史。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ModelMigrationOutcome` | enum | 迁移结果：接受/拒绝/退出 |
| `ModelMigrationCopy` | struct | 迁移提示的标题、内容和可选 Markdown |
| `MigrationMenuOption` | enum | 菜单选项：尝试新模型/使用现有模型 |
| `ModelMigrationScreen` | struct | 迁移提示界面的状态 |
| `AltScreenGuard` | struct | RAII 守卫，自动进入/离开备用屏幕 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `migration_copy_for_models` | `(current, target, ...) -> ModelMigrationCopy` | 构建迁移提示内容 |
| `run_model_migration_prompt` | `async (tui, copy) -> Outcome` | 运行迁移提示事件循环 |
| `fill_migration_markdown` | `(template, from, to) -> String` | 填充 Markdown 模板变量 |

## 依赖关系

- **引用的模块**: `crate::markdown_render`, `crate::selection_list`, `crate::tui`, `crate::key_hint`
- **引用的 crate**: `crossterm`, `ratatui`, `tokio_stream`

## 实现备注

- 使用 `AltScreenGuard` 的 RAII 模式确保离开时恢复主屏幕。
- 支持上/下/j/k 导航、Enter/Esc 确认、数字键快速选择、Ctrl+C/D 退出。
- 当 `can_opt_out` 为 false 时，只显示确认按钮，不显示菜单。
