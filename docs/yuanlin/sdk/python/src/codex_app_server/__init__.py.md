# `__init__.py` -- codex_app_server 包的公共 API 入口

## 功能概述

本模块是 `codex_app_server` Python SDK 的顶层包入口，汇集并重新导出了所有公共 API 符号。它从各子模块中导入客户端类、高级 API 类、错误类型、生成的协议类型以及工具函数，提供统一的导入路径供使用者消费。当前 SDK 版本为 `0.2.0`。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `Codex` | 类（重导出） | 同步高级 SDK 客户端 |
| `AsyncCodex` | 类（重导出） | 异步高级 SDK 客户端 |
| `AppServerClient` | 类（重导出） | 低级同步 JSON-RPC 客户端 |
| `AsyncAppServerClient` | 类（重导出） | 低级异步 JSON-RPC 客户端 |
| `AppServerConfig` | 类（重导出） | 客户端配置数据类 |
| `Thread` / `AsyncThread` | 类（重导出） | 线程（对话）句柄 |
| `TurnHandle` / `AsyncTurnHandle` | 类（重导出） | 轮次（turn）句柄 |
| `RunResult` | 类（重导出） | 便捷 run 调用的结果容器 |
| `TextInput` / `ImageInput` / `LocalImageInput` / `SkillInput` / `MentionInput` | 类（重导出） | 输入类型 |
| `InitializeResponse` | 类（重导出） | 初始化响应模型 |
| `retry_on_overload` | 函数（重导出） | 过载重试辅助函数 |
| 各种 `*Error` 类 | 异常类（重导出） | SDK 错误层次结构 |
| 生成的 v2 协议类型 | 类（重导出） | `ThreadStartParams`、`TurnStatus` 等生成的 Pydantic 模型 |

## 依赖关系

- **引用的模块**: `.async_client`, `.client`, `.errors`, `.generated.v2_all`, `.models`, `.api`, `.retry`
- **被引用方**: 所有示例文件、测试文件以及外部使用者

## 实现备注

- `__all__` 列表完整列举了所有公共导出符号，确保 `from codex_app_server import *` 行为可控
- 版本号 `__version__` 在此处硬编码，发布时由构建脚本覆写
