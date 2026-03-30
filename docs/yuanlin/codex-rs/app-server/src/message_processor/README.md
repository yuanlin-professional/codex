# message_processor

## 功能概述

`message_processor/` 子目录是 `MessageProcessor` 模块的辅助子模块，当前仅包含分布式追踪（tracing）相关的集成测试。该子模块验证 app-server 在处理请求时能正确传播 OpenTelemetry 追踪上下文，确保跨服务的请求链路可以被完整追踪。

这些测试验证了当客户端通过 JSON-RPC 请求携带 W3C Trace Context（`traceparent` / `tracestate`）时，app-server 能正确地：
- 继承远程父 Span 的 trace ID
- 将请求处理、线程启动、轮次启动等操作关联到正确的追踪链路
- 在 Span 属性中记录 RPC 方法名、传输方式等元数据

## 目录结构

```
message_processor/
└── tracing_tests.rs            # OpenTelemetry 追踪传播集成测试
```

## 核心接口/API

### tracing_tests.rs - 追踪传播测试

该文件包含一系列端到端的追踪测试，使用 `InMemorySpanExporter` 捕获生成的 Span 数据进行验证：

- **`TestTracing`** - 测试追踪基础设施，包含内存 Span 导出器和追踪提供者
- **`RemoteTrace`** - 模拟远程追踪上下文，生成 W3C `traceparent` 和 `tracestate` 头

测试覆盖的场景包括：
- 使用 `MessageProcessor` 处理带有追踪上下文的 `initialize`、`thread/start`、`turn/start` 请求
- 验证生成的 Span 是否正确继承了远程 trace ID 和 parent span ID
- 验证 Span 的 `SpanKind`（SERVER）、名称和属性是否符合预期
- 使用 mock 模型服务器模拟完整的请求-响应流程
