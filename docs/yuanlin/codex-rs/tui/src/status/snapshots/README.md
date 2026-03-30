# src/status/snapshots/

## 功能概述

`snapshots/` 目录存放 `status` 模块的 `insta` 渲染快照测试文件。这些快照用于验证状态卡片和速率限制显示在不同数据条件下的格式化输出是否正确。

## 目录结构

```
snapshots/
└── *.snap    # insta 快照文件
```

## 核心接口/API

无独立 API。快照文件由 `cargo insta` 测试框架自动生成和验证。
