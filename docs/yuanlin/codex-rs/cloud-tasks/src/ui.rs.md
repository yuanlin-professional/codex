# `ui.rs` — Cloud Tasks TUI 渲染模块

## 功能概述
实现基于 ratatui 的终端界面渲染逻辑，包括任务列表视图、新建任务页面、diff 详情覆盖层、环境选择模态框、best-of-N 模态框和 apply 确认模态框。支持 diff 语法高亮、对话内容渲染、行内/居中加载动画，以及滚动百分比显示。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `draw` | `fn(frame, app)` | TUI 主绘制入口，根据应用状态分派到不同视图 |
| `draw_new_task_page` | `fn(frame, area, app)` | 绘制新建任务编辑页面 |
| `draw_diff_overlay` | `fn(frame, area, app)` | 绘制 diff/对话详情覆盖层 |
| `draw_env_modal` | `fn(frame, area, app)` | 绘制环境选择模态框 |
| `draw_best_of_modal` | `fn(frame, area, app)` | 绘制并行尝试次数选择模态框 |
| `draw_apply_modal` | `fn(frame, area, app)` | 绘制 apply 确认/结果模态框 |

## 依赖关系
- **引用的 crate**: `ratatui`, `codex_cloud_tasks_client`, `codex_tui`
- **被引用方**: TUI 事件循环主模块

## 实现备注
支持圆角边框（通过 `CODEX_TUI_ROUNDED` 环境变量控制）、diff 行着色（+绿/-红/@@品红）、对话角色标注（User/Assistant）和 Markdown 文本内联渲染。
