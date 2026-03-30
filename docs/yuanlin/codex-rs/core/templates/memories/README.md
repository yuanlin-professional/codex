# templates/memories/ -- 记忆系统模板

## 功能概述

`memories/` 包含 Codex 记忆系统两阶段管道使用的提示词模板。这些模板指导 LLM 从原始对话记录中提取有用记忆、读取特定路径的信息，以及将多个原始记忆合并为统一摘要。记忆系统帮助未来的 AI 代理深入理解用户偏好、复用已验证的工作流、避免已知问题。

## 目录结构

```
memories/
├── stage_one_system.md     # Phase 1 系统提示词：从单个 rollout 提取原始记忆
├── stage_one_input.md      # Phase 1 输入模板
├── read_path.md            # 记忆路径读取模板
└── consolidation.md        # Phase 2 合并提示词：多个原始记忆合并为统一摘要
```

## 依赖关系

- **被引用方**：`src/memories/phase1.rs`（Phase 1 引用 `stage_one_system.md` 和 `stage_one_input.md`）、`src/memories/phase2.rs`（Phase 2 引用 `consolidation.md`）

## 核心接口/API

| 文件 | 说明 |
|---|---|
| `stage_one_system.md` | Phase 1 系统提示词：定义记忆写入代理的目标（理解用户、减少 token 消耗、复用工作流、避免陷阱）以及严格的安全和卫生规则 |
| `stage_one_input.md` | Phase 1 输入格式模板 |
| `read_path.md` | 记忆路径读取指令模板 |
| `consolidation.md` | Phase 2 合并提示词：将多个原始记忆合并为统一的记忆摘要 |
