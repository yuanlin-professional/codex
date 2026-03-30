# tests/ -- 集成测试

## 功能概述

`tests/` 是 Codex core crate 的集成测试目录。它包含一个完整的测试基础设施，用于端到端测试 Codex 的各项功能：会话管理、工具执行、模型交互、MCP 集成、多代理协作、代码审查、上下文压缩等。测试通过模拟 OpenAI Responses API 服务器来验证行为，无需实际网络调用。

## 目录结构

```
tests/
├── all.rs                         # 测试入口文件，聚合所有测试模块
├── responses_headers.rs           # Responses API 响应头构建辅助
├── cli_responses_fixture.sse      # CLI Responses SSE 流测试数据
├── common/                        # 测试公共库
├── fixtures/                      # 测试 fixture 数据
└── suite/                         # 测试套件（所有具体测试用例）
```

## 依赖关系

- **上游依赖**：`insta`（快照测试）、`tempfile`（临时目录）、`regex-lite`（正则匹配）、`codex-core`（被测目标）
- **内部依赖**：`common/`（测试辅助库）、`fixtures/`（测试数据）
- **被依赖方**：CI/CD 管道运行测试

## 核心接口/API

| 文件/目录 | 说明 |
|---|---|
| `all.rs` | 测试入口，聚合 `suite/` 下的所有测试模块 |
| `common/` | 测试公共库（配置构建、模拟服务器、SSE 流处理等） |
| `fixtures/` | 测试 fixture 数据文件 |
| `suite/` | 全部具体测试用例 |
