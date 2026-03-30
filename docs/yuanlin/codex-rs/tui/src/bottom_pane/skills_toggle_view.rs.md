# `skills_toggle_view.rs` -- 技能启用/禁用切换视图

## 功能概述
该文件实现了技能启用/禁用切换视图（SkillsToggleView），允许用户通过交互式列表查看和切换技能的启用状态。支持模糊搜索过滤、键盘导航和空格/Enter 切换操作。关闭视图时会触发技能列表刷新。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SkillsToggleItem` | 结构体 | 技能条目，包含名称、技能名、描述、启用状态和路径 |
| `SkillsToggleView` | 结构体 | 技能切换视图，包含项目列表、搜索过滤和滚动状态 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(items, app_event_tx) -> Self` | 创建技能切换视图 |
| `toggle_selected` | `fn toggle_selected(&mut self)` | 切换选中技能的启用状态并发送 SetSkillEnabled 事件 |
| `close` | `fn close(&mut self)` | 关闭视图，发送 ManageSkillsClosed 并强制刷新技能列表 |
| `apply_filter` | `fn apply_filter(&mut self)` | 应用搜索过滤 |

## 依赖关系
- **引用的 crate**: `crossterm`、`ratatui`
- **被引用方**: `mod.rs` 导出 `SkillsToggleItem` 和 `SkillsToggleView`

## 实现备注
- 使用 `match_skill` 辅助函数进行模糊匹配（同时匹配显示名和技能名）
- 关闭时发送 `list_skills(force_reload=true)` 确保技能列表刷新
- 使用 `render_rows_single_line` 进行单行渲染
