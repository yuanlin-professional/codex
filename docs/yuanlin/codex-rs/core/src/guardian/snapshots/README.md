# guardian/snapshots/ -- Guardian 测试快照

## 功能概述

`snapshots/` 存放 Guardian 模块测试的 `insta` 快照文件。这些快照验证 Guardian 审批请求的序列化布局在代码变更后保持一致。

## 目录结构

```
snapshots/
├── codex_core__guardian__tests__guardian_review_request_layout.snap            # 审批请求布局快照
└── codex_core__guardian__tests__guardian_followup_review_request_layout.snap   # 后续审批请求布局快照
```

## 依赖关系

- **上游依赖**：`insta`（快照测试框架）
- **被依赖方**：`guardian/tests.rs`（Guardian 测试验证快照一致性）

## 核心接口/API

无公开接口。此目录仅存储 insta 快照文件。
