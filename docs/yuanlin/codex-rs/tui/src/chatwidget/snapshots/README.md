# src/chatwidget/snapshots/

## 功能概述

`snapshots/` 目录存放 `chatwidget` 模块的 `insta` 渲染快照测试文件。这些快照用于验证聊天组件在各种状态下（消息渲染、工具调用显示、中断处理等）的渲染输出是否正确。

## 目录结构

```
snapshots/
└── *.snap    # insta 快照文件
```

## 核心接口/API

无独立 API。快照文件由 `cargo insta` 测试框架自动生成和验证。
