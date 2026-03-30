# codex/ -- 会话核心

## 功能概述

`codex/` 是 Codex 核心会话（Session）的实现目录。它包含 `Session` 对象的主要逻辑以及从 rollout 文件重建对话历史的功能。`Session` 是 Codex 运行时的中心对象，协调配置、模型调用、工具执行、状态管理和事件发射。rollout 重建功能用于会话恢复（resume）和分叉（fork）场景。

## 目录结构

```
codex/
├── rollout_reconstruction.rs        # 从 rollout 文件重建对话历史和轮次设置
└── rollout_reconstruction_tests.rs  # rollout 重建测试
```

## 依赖关系

- **上游依赖**：`codex-protocol`（`ResponseItem`、`TurnContextItem`、`PreviousTurnSettings`）
- **内部依赖**：`context_manager/`（`is_user_turn_boundary`）、`state/`（SessionState）
- **被依赖方**：Session 的 resume/fork 流程使用 rollout 重建

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `RolloutReconstruction` | rollout 重建结果：重建的历史、前一轮次设置、参考上下文项 |
| `reconstruct_history_from_rollout()` | 从 rollout 项列表重建完整对话历史和元数据 |
