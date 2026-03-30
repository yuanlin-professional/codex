# `assistant_text.rs` — 助手文本流解析器

## 功能概述

组合 `CitationStreamParser` 和 `ProposedPlanParser`，在单次处理中解析助手文本流标记：剥离 `<oai-mem-citation>` 标签并提取引用内容，在计划模式下额外剥离 `<proposed_plan>` 块并发射计划段。`AssistantTextChunk` 包含可见文本、引用列表和计划段列表。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AssistantTextChunk` | struct | 解析结果：visible_text、citations、plan_segments |
| `AssistantTextStreamParser` | struct | 组合解析器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(plan_mode: bool) -> Self` | 创建解析器 |
| `push_str` | `fn(&mut self, chunk) -> AssistantTextChunk` | 推入文本块 |
| `finish` | `fn(&mut self) -> AssistantTextChunk` | 刷新缓冲状态 |

## 依赖关系

- **引用的 crate**: 本 crate 内的 `CitationStreamParser`, `ProposedPlanParser`
- **被引用方**: TUI 和消息渲染层

## 实现备注

- 引用剥离优先于计划解析：先通过 citation parser 获取可见文本，再传入 plan parser。
- 测试验证了跨块边界的引用解析和引用+计划混合场景。
