# utils/ -- 工具函数

## 功能概述

`utils/` 是一个轻量级工具函数模块，作为外部 `codex-utils-path` crate 的重导出层。它提供路径处理等通用工具函数，供 core crate 内部使用。

## 目录结构

```
utils/
├── mod.rs         # 模块入口，导出 path_utils 子模块
└── path_utils.rs  # 重导出 codex_utils_path crate 的所有内容
```

## 依赖关系

- **上游依赖**：`codex-utils-path`（路径处理工具函数）
- **内部依赖**：无
- **被依赖方**：core crate 内部其他模块在需要路径工具函数时使用

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `path_utils::*` | 重导出自 `codex-utils-path` 的所有路径处理工具函数 |
