# hooks/src — 源代码目录

## 功能概述
hooks crate 的源代码实现目录。实现钩子系统，支持在关键事件时执行自定义命令。

## 目录结构
| 文件 | 说明 |
|------|------|
| `legacy_notify.rs` | 遗留通知机制 |
| `lib.rs` | 库入口 |
| `registry.rs` | 钩子注册表 |
| `schema.rs` | Schema 定义 |
| `types.rs` | 类型定义 |
| `user_notification.rs` | 用户通知 |
