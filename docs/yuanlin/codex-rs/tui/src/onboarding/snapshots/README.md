# src/onboarding/snapshots/

## 功能概述

`snapshots/` 目录存放 `onboarding` 模块的 `insta` 渲染快照测试文件。这些快照用于验证引导流程界面（欢迎页、认证页、信任决策页）在不同状态下的渲染输出是否正确。

## 目录结构

```
snapshots/
└── *.snap    # insta 快照文件
```

## 核心接口/API

无独立 API。快照文件由 `cargo insta` 测试框架自动生成和验证。
