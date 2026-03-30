# `app.rs` — Cloud Tasks TUI 应用状态模型

## 功能概述
定义 Cloud Tasks TUI 的核心应用状态结构体 `App` 及其关联类型，包括任务列表、diff 覆盖层、环境过滤/模态框、新建任务页面、apply 模态框等。同时定义异步事件枚举 `AppEvent` 用于后台任务与 UI 事件循环的通信。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `App` | 结构体 | TUI 主状态：任务列表、选中索引、覆盖层、模态框、加载状态等 |
| `DiffOverlay` | 结构体 | diff 详情覆盖层：标题、滚动 diff、多尝试视图 |
| `AttemptView` | 结构体 | 单次尝试的 diff/文本/状态视图 |
| `DetailView` | 枚举 | Diff 或 Prompt 视图切换 |
| `AppEvent` | 枚举 | 后台任务事件：任务加载完成、详情加载、环境检测等 |
| `ApplyModalState` | 结构体 | apply 确认模态框状态 |

## 依赖关系
- **引用的 crate**: `codex_cloud_tasks_client`
- **被引用方**: `ui.rs`, `lib.rs`

## 实现备注
包含 `FakeBackend` 测试实现，验证 `load_tasks` 函数能正确传递环境过滤参数。
