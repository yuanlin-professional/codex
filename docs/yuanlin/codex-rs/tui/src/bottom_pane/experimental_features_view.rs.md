# `experimental_features_view.rs` -- 实验性功能切换视图

## 功能概述
该文件实现了实验性功能的切换视图（ExperimentalFeaturesView），允许用户查看和切换实验性功能的开关状态。更改会自动保存到 config.toml 配置文件。视图支持键盘导航（Up/Down/j/k）和空格键切换选中项目的启用状态。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ExperimentalFeatureItem` | 结构体 | 实验性功能条目，包含 Feature 枚举、名称、描述和启用状态 |
| `ExperimentalFeaturesView` | 结构体 | 实验性功能视图，包含功能列表、滚动状态和事件发送器 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(features, app_event_tx) -> Self` | 创建新的实验性功能视图 |
| `toggle_selected` | `fn toggle_selected(&mut self)` | 切换当前选中项目的启用状态 |
| `on_ctrl_c` | `fn on_ctrl_c(&mut self) -> CancellationEvent` | 关闭视图并发送 `UpdateFeatureFlags` 事件保存更改 |

## 依赖关系
- **引用的 crate**: `codex_features`（Feature）、`crossterm`、`ratatui`
- **被引用方**: `mod.rs` 导出 `ExperimentalFeatureItem` 和 `ExperimentalFeaturesView`

## 实现备注
- 显示复选框样式 `[x]`/`[ ]` 标示启用/禁用状态
- 关闭时（Enter/Esc/Ctrl+C）批量发送所有功能标志更新
- 使用共享的 `ScrollState` 和 `render_rows` 进行滚动列表渲染
