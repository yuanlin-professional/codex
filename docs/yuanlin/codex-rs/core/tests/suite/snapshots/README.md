# tests/suite/snapshots/ -- 测试快照

## 功能概述

`snapshots/` 存放 `suite/` 测试套件生成的 `insta` 快照文件。每个 `.snap` 文件对应一个测试用例的期望输出，用于回归测试。当代码变更导致输出变化时，开发者需要审查并更新快照。快照文件按 `{crate}__{module}__{test_name}.snap` 格式命名。

## 目录结构

```
snapshots/
├── all__suite__compact__manual_compact_with_history_shapes.snap
├── all__suite__compact__mid_turn_compaction_shapes.snap
├── all__suite__compact_remote__remote_compact_resume_restates_realtime_end_shapes.snap
├── all__suite__compact_remote__remote_manual_compact_with_history_shapes.snap
└── ... (大量快照文件，覆盖 compact、compact_remote 等测试)
```

## 依赖关系

- **上游依赖**：`insta`（快照测试框架）
- **被依赖方**：`tests/suite/` 中的测试用例验证输出与快照是否一致

## 核心接口/API

无公开接口。此目录仅存储 insta 快照文件，用于自动化回归测试验证。

文件命名规范：`all__suite__{module}__{test_function_name}.snap`
