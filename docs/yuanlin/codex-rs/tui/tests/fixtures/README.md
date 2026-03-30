# tests/fixtures/

## 功能概述

`fixtures/` 目录存放集成测试使用的固定测试数据文件。这些文件为测试提供预定义的输入场景，确保测试结果可重现。

## 目录结构

```
fixtures/
└── oss-story.jsonl    # OSS 场景测试数据（JSONL 格式的模拟事件序列）
```

## 核心接口/API

无独立 API。测试数据文件由集成测试在运行时读取。
