# templates/compact/ -- 上下文压缩模板

## 功能概述

`compact/` 包含 Codex 上下文压缩（Compaction）功能使用的提示词模板。当对话上下文超过模型的 token 限制时，系统会调用压缩任务将历史对话压缩为精简摘要。这些模板指导 LLM 生成高质量的交接摘要，确保压缩后的上下文保留关键信息。

## 目录结构

```
compact/
├── prompt.md            # 压缩提示词：指导 LLM 生成上下文检查点摘要
└── summary_prefix.md    # 摘要前缀模板
```

## 依赖关系

- **被引用方**：`src/tasks/compact.rs`（CompactTask 使用压缩提示词）

## 核心接口/API

| 文件 | 说明 |
|---|---|
| `prompt.md` | 上下文检查点压缩提示词，要求包含：当前进度和关键决策、重要约束和用户偏好、剩余工作（清晰的下一步）、继续工作所需的关键数据和引用 |
| `summary_prefix.md` | 压缩摘要输出的前缀模板 |
