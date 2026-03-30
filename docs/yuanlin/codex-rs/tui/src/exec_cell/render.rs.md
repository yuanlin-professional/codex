# `render.rs` — 执行单元渲染逻辑

## 功能概述

本模块负责将 `ExecCell` 数据模型渲染为 TUI 中的可显示行。它处理两种显示模式：探索型命令的紧凑展示（Read/Search/List 分组）和普通命令的标准展示（带语法高亮的 bash 脚本、输出截断、状态指示器）。还提供了 spinner 动画和输出行的截断/省略逻辑。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `OutputLinesParams` | struct | 输出行生成参数：行数限制、是否仅错误、是否包含前缀等 |
| `OutputLines` | struct | 生成的输出行和省略行数 |
| `ExecDisplayLayout` | struct | 命令显示布局配置：续行、输出块的前缀和最大行数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new_active_exec_command` | `(...) -> ExecCell` | 创建一个新的活跃命令执行单元格 |
| `output_lines` | `(output, params) -> OutputLines` | 将命令输出格式化为显示行（支持头尾截断） |
| `spinner` | `(start_time, animations_enabled) -> Span` | 生成 spinner 动画 span |
| `display_lines` | `(&self, width) -> Vec<Line>` | 根据单元格类型选择探索或命令显示模式 |
| `truncate_lines_middle` | `(lines, max_rows, width, ...) -> Vec<Line>` | 基于视口行数的中间截断 |

## 依赖关系

- **引用的 crate**: `ratatui`, `codex_ansi_escape`, `codex_shell_command`, `textwrap`
- **被引用方**: `HistoryCell` 实现，`ChatWidget` 命令显示

## 实现备注

- 视口行数截断（不是逻辑行截断）防止长 URL 占用过多空间
- 探索型命令将连续的 Read 调用合并为单行展示
- 用户 shell 命令的输出上限为 50 行，代理命令为 5 行
- spinner 在支持 16M 色的终端使用 shimmer 效果，否则使用简单闪烁
