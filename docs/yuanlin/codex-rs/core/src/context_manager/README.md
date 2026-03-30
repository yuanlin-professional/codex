# context_manager/ -- 上下文管理器

## 功能概述

`context_manager/` 负责管理 Codex 会话的对话历史和上下文状态。核心功能包括：维护对话项（`ResponseItem`）的有序列表、跟踪 token 使用情况、执行历史裁剪和回滚、对大型工具输出进行截断处理，以及在轮次之间生成增量上下文更新（环境变更、权限变更、协作模式变更等）。它是确保 AI 模型在每次推理时获得正确、精简上下文的关键组件。

## 目录结构

```
context_manager/
├── mod.rs              # 模块入口，导出核心类型
├── history.rs          # ContextManager：对话历史管理器（记录、裁剪、token 估算、回滚）
├── normalize.rs        # 历史条目规范化处理
├── updates.rs          # 轮次间增量上下文更新构建（环境/权限/协作模式差异对比）
└── history_tests.rs    # 历史管理器测试
```

## 依赖关系

- **上游依赖**：`codex-protocol`（`ResponseItem`、`TokenUsage`、`TurnContextItem`）、`codex-utils-output-truncation`（token 估算和截断策略）、`codex-utils-cache`（LRU 缓存和 SHA1 摘要）
- **内部依赖**：`event_mapping`（消息内容分类）、`environment_context`（环境上下文差异）、`codex/`（TurnContext）
- **被依赖方**：`state/`（SessionState 持有 ContextManager）、`tasks/`（任务执行时读写历史）、`codex/`（Session 通过 state 操作历史）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `ContextManager` | 对话历史管理器，管理 items 列表、token 信息和参考上下文快照 |
| `TotalTokenUsageBreakdown` | token 使用分解：最近 API 响应总 token、全部历史项可见字节、增量估算等 |
| `record_items()` | 将新对话项追加到历史（带截断策略） |
| `rollback_to()` | 回滚历史到指定位置 |
| `estimate_response_item_model_visible_bytes()` | 估算单个对话项对模型可见的字节数 |
| `is_user_turn_boundary()` | 判断某项是否为用户轮次边界 |
| `build_environment_update_item()` | 构建环境变更差异项 |
| `build_permissions_update_item()` | 构建权限变更差异项 |
