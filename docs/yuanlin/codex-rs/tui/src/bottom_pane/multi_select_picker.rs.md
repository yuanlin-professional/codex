# `multi_select_picker.rs` -- 多选选择器组件

## 功能概述
该文件实现了一个通用的多选选择器组件（MultiSelectPicker），支持模糊搜索过滤、项目切换、排序调整和实时预览。采用 Builder 模式构造，通过回调函数处理变更、确认和取消事件。被状态行配置和终端标题配置视图复用。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `MultiSelectItem` | 结构体 | 多选项目，包含 id、名称、描述和启用状态 |
| `MultiSelectPicker` | 结构体 | 多选选择器主组件 |
| `MultiSelectPickerBuilder` | 结构体 | Builder 模式构造器 |
| `ChangeCallBack` / `ConfirmCallback` / `CancelCallback` / `PreviewCallback` | 类型别名 | 各种事件回调类型 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `builder` | `fn builder(title, subtitle, tx) -> MultiSelectPickerBuilder` | 创建 Builder |
| `apply_filter` | `fn apply_filter(&mut self)` | 应用模糊搜索过滤并按得分排序 |
| `toggle_selected` | `fn toggle_selected(&mut self)` | 切换当前选中项目的启用状态 |
| `move_selected_item` | `fn move_selected_item(&mut self, direction)` | 上下移动选中项目在列表中的位置 |
| `confirm_selection` | `fn confirm_selection(&mut self)` | 确认选择，收集启用项的 ID 并调用回调 |
| `match_item` | `fn match_item(filter, display_name, name) -> Option<...>` | 对项目执行模糊匹配 |

## 依赖关系
- **引用的 crate**: `codex_utils_fuzzy_match`、`crossterm`、`ratatui`
- **被引用方**: `StatusLineSetupView` 和 `TerminalTitleSetupView` 内部使用

## 实现备注
- 搜索为空时排序不启用，搜索有内容时按模糊匹配得分排序
- 左右箭头重排仅在搜索为空时可用
- 项目名称超过 21 字符时自动截断
- 预览行在每次状态变更时重新生成
