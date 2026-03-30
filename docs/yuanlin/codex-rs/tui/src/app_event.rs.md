# `app_event.rs` — 应用层事件总线定义

## 功能概述

本文件定义了 `AppEvent` 枚举，作为 TUI 组件之间以及组件与顶层 `App` 循环之间的内部消息总线。各个 widget 通过发送 `AppEvent` 来请求必须在 app 层处理的操作（如打开选择器、持久化配置、关闭应用等），而无需直接访问 `App` 内部状态。

退出通过 `AppEvent::Exit(ExitMode)` 显式建模，调用方可以请求"先关闭再退出"或"立即退出"两种策略，实现了退出/关闭序列的解耦。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppEvent` | 枚举 | 应用级事件，涵盖线程操作、文件搜索、插件管理、模型选择、权限配置、反馈等约60个变体 |
| `ExitMode` | 枚举 | 退出策略：`ShutdownFirst`（先清理后退出）或 `Immediate`（立即退出） |
| `FeedbackCategory` | 枚举 | 反馈类别：BadResult / GoodResult / Bug / SafetyCheck / Other |
| `RealtimeAudioDeviceKind` | 枚举 | 实时音频设备类型：Microphone / Speaker |
| `WindowsSandboxEnableMode` | 枚举 | Windows 沙箱启用模式：Elevated / Legacy |
| `ConnectorsSnapshot` | 结构体 | 连接器快照，包含可用 AppInfo 列表 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（Op、各协议类型）、`codex_app_server_protocol`（插件、MCP 相关类型）、`codex_file_search`、`codex_features`
- **被引用方**: 几乎所有 TUI 模块（`app.rs`、`chatwidget.rs`、`app_event_sender.rs` 等）通过此事件总线通信

## 实现备注

许多 Windows 特有的变体使用 `#[cfg_attr(not(target_os = "windows"), allow(dead_code))]` 注解，确保跨平台编译不会产生警告。事件枚举设计为只持有数据，不包含行为逻辑，所有处理集中在 `app.rs` 的事件循环中。
