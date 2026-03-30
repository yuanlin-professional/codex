# src/bottom_pane/request_user_input/snapshots/

## 功能概述

`snapshots/` 目录存放 `request_user_input` 模块的 `insta` 渲染快照测试文件。这些快照用于验证用户输入请求覆盖层在不同状态下的渲染输出是否正确。

## 目录结构

```
snapshots/
└── *.snap    # insta 快照文件
```

## 核心接口/API

无独立 API。快照文件由 `cargo insta` 测试框架自动生成和验证。
