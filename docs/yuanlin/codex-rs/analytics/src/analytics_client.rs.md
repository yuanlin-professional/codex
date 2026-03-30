# `analytics_client.rs` — 分析事件客户端

## 功能概述
实现 Codex 分析事件的异步发送系统。通过 mpsc 队列将事件从业务逻辑解耦到后台发送任务。支持技能调用（skill invocation）、应用提及/使用（app mentioned/used）和插件使用/安装/卸载/启用/禁用等事件类型。包含按 turn+connector/plugin 的去重机制。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `AnalyticsEventsClient` | 结构体 | 分析事件客户端，封装队列和启用状态 |
| `TrackEventsContext` | 结构体 | 事件上下文：model_slug, thread_id, turn_id |
| `SkillInvocation` | 结构体 | 技能调用信息：名称、范围、路径、调用类型 |
| `AppInvocation` | 结构体 | 应用调用信息：connector_id, app_name, invocation_type |
| `InvocationType` | 枚举 | Explicit 或 Implicit |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `track_skill_invocations` | `fn(&self, ctx, invocations)` | 发送技能调用事件 |
| `track_app_used` | `fn(&self, ctx, app)` | 发送应用使用事件（去重） |
| `track_plugin_installed` | `fn(&self, plugin)` | 发送插件安装事件 |

## 依赖关系
- **引用的 crate**: `codex_login`, `codex_git_utils`, `codex_plugin`, `sha1`
- **被引用方**: codex-core 事件跟踪

## 实现备注
事件发送失败不阻塞主流程。队列满时丢弃事件。去重缓存上限 4096 个 key，超出时清空重建。含 10+ 测试用例验证序列化格式和去重逻辑。
