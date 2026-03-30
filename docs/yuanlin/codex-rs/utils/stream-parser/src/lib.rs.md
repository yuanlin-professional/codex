# `lib.rs` — 流解析器模块入口

本文件是 `stream-parser` 子 crate 的入口模块，声明并重新导出所有子模块的公共类型：

- `AssistantTextChunk`, `AssistantTextStreamParser`：助手文本解析
- `CitationStreamParser`, `strip_citations`：引用标签处理
- `ExtractedInlineTag`, `InlineHiddenTagParser`, `InlineTagSpec`：通用隐藏标签解析
- `ProposedPlanParser`, `ProposedPlanSegment`, `extract_proposed_plan_text`, `strip_proposed_plan_blocks`：计划块解析
- `StreamTextChunk`, `StreamTextParser`：流文本核心 trait
- `Utf8StreamParser`, `Utf8StreamParserError`：UTF-8 字节流封装
