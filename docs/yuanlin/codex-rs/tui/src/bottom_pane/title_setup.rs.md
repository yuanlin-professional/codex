# `title_setup.rs` -- 终端标题配置视图

## 功能概述
该文件实现了终端标题配置视图（TerminalTitleSetupView），允许用户选择和排序在终端窗口/标签标题中显示的信息项。支持 8 种标题项（AppName、Project、Spinner、Status、Thread、GitBranch、Model、TaskProgress），使用 MultiSelectPicker 组件提供选择、排序和实时预览功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `TerminalTitleItem` | 枚举 | 8 种可配置的标题项 |
| `TerminalTitleSetupView` | 结构体 | 包装 MultiSelectPicker 的标题配置视图 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(title_items, app_event_tx) -> Self` | 创建配置视图 |
| `description` | `fn description(self) -> &'static str` | 获取项目的用户可见描述 |
| `preview_example` | `fn preview_example(self) -> &'static str` | 获取项目的预览示例文本 |
| `separator_from_previous` | `fn separator_from_previous(self, previous) -> &'static str` | 获取与前一项之间的分隔符 |

## 依赖关系
- **引用的 crate**: `itertools`、`strum`（EnumIter, EnumString, Display）、`ratatui`
- **被引用方**: `mod.rs` 导出 `TerminalTitleItem` 和 `TerminalTitleSetupView`

## 实现备注
- Spinner 项与其他项之间使用空格分隔，其余项之间使用 ` | `
- 预览使用静态示例值（如 "codex"、"my-project"、"feat/awesome-feature"），非实时数据
- on_change 回调在每次修改时发送预览事件以实时更新标题
- 枚举使用 kebab-case 序列化，变更标识符是破坏性配置变更
