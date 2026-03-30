# analytics

## 功能概述

`codex-analytics` 是 Codex 项目的使用分析（Usage Analytics）模块，负责收集和上报用户会话中的关键事件数据。它跟踪应用调用（App Invocation）、技能调用（Skill Invocation）等行为指标，通过异步事件队列将遥测数据发送到后端分析服务，用于产品改进和使用洞察。

## 架构说明

该 crate 采用异步事件队列架构：

1. **AnalyticsEventsClient** - 面向外部的客户端接口，负责接收和调度分析事件。
2. **AnalyticsEventsQueue** - 内部事件队列，使用 `tokio::sync::mpsc` 通道异步处理事件上报任务。
3. **TrackEventsContext** - 事件上下文，包含模型标识（model_slug）、线程 ID（thread_id）和轮次 ID（turn_id）等元数据。

事件队列会对重复事件进行去重（通过 `HashSet` 维护已上报的 app/plugin 键），避免重复上报。认证信息通过 `codex-login` 的 `AuthManager` 获取，确保上报请求的合法性。

## 目录结构

```
analytics/
  Cargo.toml
  src/
    lib.rs                      # 模块入口，导出公共 API
    analytics_client.rs         # 核心分析客户端实现
    analytics_client_tests.rs   # 客户端单元测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-git-utils` | 获取 Git 仓库信息用于上下文收集 |
| `codex-login` | 认证管理器，用于获取上报所需的认证令牌 |
| `codex-plugin` | 插件遥测元数据定义 |
| `codex-protocol` | 协议定义（如 SkillScope） |
| `serde` | 事件数据的序列化 |
| `sha1` | 数据哈希处理 |
| `tokio` | 异步运行时，驱动事件队列 |
| `tracing` | 日志追踪 |

## 核心接口/API

### 导出类型

- **`AnalyticsEventsClient`** - 分析事件客户端，提供事件上报入口。
- **`TrackEventsContext`** - 事件跟踪上下文，包含：
  - `model_slug: String` - 模型标识
  - `thread_id: String` - 会话线程 ID
  - `turn_id: String` - 对话轮次 ID
- **`AppInvocation`** - 应用调用事件数据。
- **`SkillInvocation`** - 技能调用事件数据，包含技能名称、作用域、路径和调用类型。
- **`InvocationType`** - 调用类型枚举：`Explicit`（显式）/ `Implicit`（隐式）。

### 工厂函数

- **`build_track_events_context(model_slug, thread_id, turn_id) -> TrackEventsContext`** - 构建事件跟踪上下文。
