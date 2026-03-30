# src/bin/

## 功能概述

`bin/` 目录包含 `codex-tui` crate 附带的辅助二进制工具。目前仅包含一个工具：`md-events`。

`md-events` 是一个 Markdown 事件调试工具，从标准输入读取 Markdown 文本，使用 `pulldown-cmark` 解析器逐一输出解析事件。这对于调试 Markdown 渲染问题非常有用。

## 目录结构

```
bin/
└── md-events.rs    # Markdown 事件调试工具
```

## 核心接口/API

| 二进制 | 用法 | 说明 |
|--------|------|------|
| `md-events` | `echo "# Hello" \| md-events` | 读取 stdin 中的 Markdown 文本，输出 pulldown-cmark 解析事件序列 |
