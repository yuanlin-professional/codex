# stream-parser

## 功能概述

`codex-utils-stream-parser` 提供了一组流式文本解析器，用于处理 AI 模型输出的流式文本。支持解析 AI 助手文本、引用标记、隐藏标签、提议计划块以及 UTF-8 字节流。

这些解析器被设计为增量工作：每次输入一小段文本，产出已解析的结构化数据块，适合处理实时流式响应。

## 目录结构

```
stream-parser/
├── Cargo.toml
└── src/
    ├── lib.rs                 # 模块导出
    ├── assistant_text.rs      # AI 助手文本流解析器
    ├── citation.rs            # 引用标记解析与剥离
    ├── inline_hidden_tag.rs   # 内联隐藏标签解析器
    ├── proposed_plan.rs       # 提议计划块解析器
    ├── stream_text.rs         # 通用流式文本解析器
    ├── tagged_line_parser.rs  # 带标签的行解析器
    └── utf8_stream.rs         # UTF-8 字节流解析器
```

## 依赖关系

无运行时依赖，仅使用标准库。开发依赖 `pretty_assertions` 用于测试。

## 核心接口/API

### `AssistantTextStreamParser` / `AssistantTextChunk`

解析 AI 助手文本流，输出文本块。

### `CitationStreamParser`

解析和管理引用标记。

```rust
pub fn strip_citations(text: &str) -> String;  // 从文本中剥离所有引用标记
```

### `InlineHiddenTagParser` / `InlineTagSpec` / `ExtractedInlineTag`

解析内联隐藏标签（如模型输出中的元数据标签）。

### `ProposedPlanParser` / `ProposedPlanSegment`

解析提议计划块。

```rust
pub fn extract_proposed_plan_text(text: &str) -> String;    // 提取计划文本
pub fn strip_proposed_plan_blocks(text: &str) -> String;     // 剥离计划块
```

### `StreamTextParser` / `StreamTextChunk`

通用的流式文本增量解析器。

### `Utf8StreamParser` / `Utf8StreamParserError`

将原始字节流解析为有效的 UTF-8 文本，处理不完整的多字节序列。
