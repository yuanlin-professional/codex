# tests/fixtures/ -- 测试 Fixture 数据

## 功能概述

`fixtures/` 存放集成测试使用的静态测试数据文件（fixture）。这些文件包含预定义的 SSE 响应流、JSON 数据等，用于在测试中模拟各种 API 响应场景，避免测试依赖外部服务。

## 目录结构

```
fixtures/
└── incomplete_sse.json    # 不完整 SSE 流测试数据（用于测试流中断处理）
```

## 依赖关系

- **被依赖方**：`tests/suite/` 中的测试用例加载 fixture 数据

## 核心接口/API

无公开接口。此目录仅存储静态测试数据文件。
