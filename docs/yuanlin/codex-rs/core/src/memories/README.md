# memories/ -- 记忆系统

## 功能概述

`memories/` 实现了 Codex 的跨会话记忆提取和合并系统。在会话启动时，它会自动从近期的 rollout（对话记录）中提取有用的记忆信息，并通过两阶段管道处理：阶段一（Phase 1）从多个 rollout 中并行提取原始记忆，阶段二（Phase 2）将原始记忆合并为统一的记忆摘要。记忆系统帮助 AI 代理在新会话中保持对项目背景和用户偏好的了解。

## 目录结构

```
memories/
├── mod.rs             # 模块入口，定义两阶段管道的常量和模块结构
├── start.rs           # start_memories_startup_task()：记忆启动管道入口
├── phase1.rs          # Phase 1：从 rollout 提取原始记忆（并行、8 并发上限）
├── phase2.rs          # Phase 2：全局锁下合并原始记忆为统一摘要
├── control.rs         # 记忆根目录管理（清除记忆内容等）
├── storage.rs         # 记忆存储操作（读写 rollout 摘要和原始记忆文件）
├── prompts.rs         # 记忆提取和合并的提示词构建
├── citations.rs       # 记忆引用处理
├── usage.rs           # 记忆工具使用指标发射
├── README.md          # 原始文档
├── tests.rs           # 集成测试
├── phase1_tests.rs    # Phase 1 测试
├── prompts_tests.rs   # 提示词测试
├── citations_tests.rs # 引用测试
└── storage_tests.rs   # 存储测试
```

## 依赖关系

- **上游依赖**：`codex-protocol`（ReasoningEffort）、`codex-api`（模型调用）
- **内部依赖**：`config/`（MemoriesConfig 配置项）、`codex/`（Session）、模板文件 `templates/memories/`
- **被依赖方**：`codex/`（Session 启动时调用 `start_memories_startup_task`）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `start_memories_startup_task()` | 记忆系统启动入口，触发两阶段提取管道 |
| `clear_memory_root_contents()` | 清除记忆根目录内容 |
| Phase 1 常量 | `MODEL="gpt-5.1-codex-mini"`、`REASONING_EFFORT=Low`、`CONCURRENCY_LIMIT=8` |
| Phase 1 限制 | `DEFAULT_STAGE_ONE_ROLLOUT_TOKEN_LIMIT=150,000` tokens |
| Phase 2 | 全局合并锁、记忆摘要生成 |
| 存储 | rollout 摘要子目录、`raw_memories.md` 文件 |
