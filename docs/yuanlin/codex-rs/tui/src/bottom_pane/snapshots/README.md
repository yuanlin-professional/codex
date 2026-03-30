# src/bottom_pane/snapshots/

## 功能概述

`snapshots/` 目录存放 `bottom_pane` 模块的 `insta` 渲染快照测试文件。这些快照用于验证底部面板各组件（输入框、审批覆盖层、弹窗等）在不同状态下的渲染输出是否正确。

## 目录结构

```
snapshots/
└── *.snap    # insta 快照文件
```

## 核心接口/API

无独立 API。快照文件由 `cargo insta` 测试框架自动生成和验证。
