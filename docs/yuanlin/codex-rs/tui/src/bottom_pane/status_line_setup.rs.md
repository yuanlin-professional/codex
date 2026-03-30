# `status_line_setup.rs` -- 状态行配置视图

## 功能概述
该文件实现了状态行配置视图（StatusLineSetupView），允许用户选择和排序在终端底部状态行中显示的信息项。支持 16 种信息项（模型名、目录、Git 分支、上下文使用量、限额等），使用 MultiSelectPicker 组件提供选择、排序和实时预览功能。配置更改通过 `StatusLineSetup` 事件持久化到 config.toml。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `StatusLineItem` | 枚举 | 16 种可配置的状态行信息项（ModelName、GitBranch、ContextRemaining 等） |
| `StatusLinePreviewData` | 结构体 | 运行时预览数据，存储各项的当前值 |
| `StatusLineSetupView` | 结构体 | 包装 MultiSelectPicker 的配置视图 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(status_line_items, preview_data, tx) -> Self` | 创建配置视图 |
| `description` | `fn description(&self) -> &'static str` | 获取项目的用户可见描述 |
| `line_for_items` | `fn line_for_items(&self, items) -> Option<Line>` | 生成预览行 |

## 依赖关系
- **引用的 crate**: `strum`（EnumIter, EnumString, Display）、`ratatui`
- **被引用方**: `mod.rs` 导出 `StatusLineItem`、`StatusLinePreviewData`、`StatusLineSetupView`

## 实现备注
- 部分项目是条件性显示的（如 Git 分支仅在 Git 仓库中显示）
- 预览使用 ` · ` 分隔各项的运行时值
- 支持左右箭头调整项目顺序
- 使用 strum 的 kebab_case 序列化用于配置文件存储
