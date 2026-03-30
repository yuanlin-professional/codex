# `command_popup.rs` -- 斜杠命令弹出窗口

## 功能概述
该文件实现了斜杠命令弹出窗口（CommandPopup），当用户在编辑器中输入 `/` 时显示可用命令列表。支持前缀过滤、精确匹配优先排序、方向键导航和命令选择。弹出窗口根据功能标志（collaboration_modes、connectors、plugins 等）动态控制命令的可见性。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CommandItem` | 枚举 | 弹出窗口中的选择项，当前仅有 `Builtin(SlashCommand)` |
| `CommandPopup` | 结构体 | 命令弹出窗口状态，包含过滤字符串、内置命令列表和滚动状态 |
| `CommandPopupFlags` | 结构体 | 控制命令可见性的功能标志集合 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(flags: CommandPopupFlags) -> Self` | 根据功能标志创建弹出窗口 |
| `on_composer_text_change` | `fn on_composer_text_change(&mut self, text: String)` | 根据编辑器文本更新过滤器 |
| `selected_item` | `fn selected_item(&self) -> Option<CommandItem>` | 获取当前选中的命令 |
| `filtered` | `fn filtered(&self) -> Vec<(CommandItem, Option<Vec<usize>>)>` | 执行精确/前缀匹配并返回结果 |
| `move_up` / `move_down` | `fn move_up/down(&mut self)` | 移动选择光标 |

## 依赖关系
- **引用的 crate**: `ratatui`、`crossterm`
- **被引用方**: `ChatComposer` 持有 `CommandPopup` 实例

## 实现备注
- 过滤算法：精确匹配优先于前缀匹配，保持原始展示顺序
- 别名命令（quit、approvals）在默认列表中隐藏，但在前缀搜索时显示
- debug 命令在弹出窗口中始终隐藏
- `/apps` 命令在弹出窗口中隐藏（通过 `$` 提及机制访问）
