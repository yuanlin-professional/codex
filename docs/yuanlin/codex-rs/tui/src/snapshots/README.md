# src/snapshots/

## 功能概述

`snapshots/` 目录存放 `codex-tui` 库级别的 `insta` 渲染快照测试文件。这些快照用于验证顶层模块（如 Markdown 渲染、差异显示、文本格式化等）的输出是否正确。

## 目录结构

```
snapshots/
└── *.snap    # insta 快照文件
```

## 核心接口/API

无独立 API。快照文件由 `cargo insta` 测试框架自动生成和验证。
