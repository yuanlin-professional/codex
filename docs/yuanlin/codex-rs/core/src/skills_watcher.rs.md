# `skills_watcher.rs` — 技能文件变更监听器

## 功能概述

此文件实现了基于通用 `FileWatcher` 的技能专用监听器。它监视技能配置文件（如 `SKILL.md`）的变更，通过节流机制避免频繁触发重载，并通过广播通道通知订阅者技能已发生变化。监听器自动注册配置中所有技能根目录的递归监视。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SkillsWatcherEvent` | 枚举 | 技能变更事件：`SkillsChanged { paths }` |
| `SkillsWatcher` | 结构体 | 技能监听器，封装 `FileWatcherSubscriber` 和广播发送端 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `SkillsWatcher::new` | `(file_watcher: &Arc<FileWatcher>) -> Self` | 创建监听器并启动事件循环 |
| `SkillsWatcher::subscribe` | `(&self) -> broadcast::Receiver<SkillsWatcherEvent>` | 订阅技能变更事件 |
| `SkillsWatcher::register_config` | `(&self, config, skills_manager, plugins_manager) -> WatchRegistration` | 注册配置中所有技能根目录的监视路径 |

## 依赖关系

- **引用的 crate**: `crate::file_watcher`（通用文件监听器）、`crate::SkillsManager`、`crate::plugins::PluginsManager`、`tokio`
- **被引用方**: 线程管理器使用此模块监听技能文件变更以触发热重载

## 实现备注

- 节流间隔：生产环境 10 秒，测试环境 50 毫秒
- 使用 `broadcast::channel(128)` 作为事件分发通道
- 事件循环通过 `Handle::try_current()` 检查是否有 Tokio 运行时，缺失时跳过监听
- `noop()` 工厂方法创建无操作的监听器实例，用于测试
