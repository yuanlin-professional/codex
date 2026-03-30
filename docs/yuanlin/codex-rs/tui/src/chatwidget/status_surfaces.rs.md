# `status_surfaces.rs` — 状态栏和终端标题渲染

## 功能概述

本模块管理 TUI 的两个状态显示面（status surfaces）：底部状态栏（status line）和终端标题（terminal title）。它解析用户配置的显示项，执行 git 分支查找等共享计算，然后分别渲染到对应的表面。终端标题支持 spinner 动画、项目名称、模型名称、任务进度等可配置项。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TerminalTitleStatusKind` | enum | 终端标题状态类型：Working、WaitingForBackgroundTerminal、Undoing、Thinking |
| `StatusSurfaceSelections` | struct | 一次刷新中解析出的状态面配置快照 |
| `CachedProjectRootName` | struct | 按 cwd 缓存的项目根目录显示名称 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `refresh_status_surfaces` | `(&mut self)` | 主刷新入口，同时更新状态栏和终端标题 |
| `status_line_value_for_item` | `(&mut self, item) -> Option<String>` | 解析单个状态栏项的显示值 |
| `terminal_title_status_text` | `(&self) -> String` | 计算终端标题的状态文本 |
| `clear_managed_terminal_title` | `(&mut self) -> io::Result<()>` | 清除 Codex 设置的终端标题 |

## 依赖关系

- **引用的 crate**: `ratatui`, `unicode_segmentation`
- **被引用方**: `ChatWidget` 在每次重绘时调用

## 实现备注

- 默认终端标题项：spinner + project
- Spinner 使用 Braille 点阵字符，100ms 间隔
- 无效配置项只警告一次，不影响其他项的显示
- 终端标题段通过 grapheme cluster 截断并添加 `...`
- git 分支查找通过异步 tokio::spawn 执行，避免阻塞渲染
