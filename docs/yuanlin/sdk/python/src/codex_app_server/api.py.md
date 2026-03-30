# `api.py` -- 高级公共 API 层（Codex / AsyncCodex / Thread / TurnHandle）

## 功能概述

本模块是 Python SDK 的核心公共 API 层，提供面向用户的高级抽象。它封装了底层 JSON-RPC 客户端，暴露出 `Codex`（同步）和 `AsyncCodex`（异步）两个入口类，以及 `Thread`/`AsyncThread`（线程句柄）和 `TurnHandle`/`AsyncTurnHandle`（轮次句柄）四个操作类。该模块中的大部分方法签名由代码生成脚本自动维护，确保与 app-server 协议 schema 保持同步。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `Codex` | 类 | 同步 SDK 入口，支持上下文管理器，初始化时启动 app-server 并验证元数据 |
| `AsyncCodex` | 类 | 异步 SDK 入口，支持 `async with`，惰性初始化（首次 API 调用或 `__aenter__` 时） |
| `Thread` | 数据类 | 同步线程句柄，提供 `run()`、`turn()`、`read()`、`set_name()`、`compact()` 方法 |
| `AsyncThread` | 数据类 | 异步线程句柄，与 `Thread` 完全对称 |
| `TurnHandle` | 数据类 | 同步轮次句柄，提供 `stream()`、`run()`、`steer()`、`interrupt()` 方法 |
| `AsyncTurnHandle` | 数据类 | 异步轮次句柄，与 `TurnHandle` 完全对称 |
| `_split_user_agent()` | 内部函数 | 解析 user-agent 字符串以提取服务器名称和版本 |

## 依赖关系

- **引用的模块**: `.async_client`, `.client`, `.generated.v2_all`（大量协议类型）, `.models`, `._inputs`, `._run`
- **被引用方**: `.__init__`（重导出）, 所有示例和测试文件

## 实现备注

- `Codex` 构造函数内同步启动子进程并完成 `initialize` 握手；若失败则自动调用 `close()` 清理资源
- `AsyncCodex` 使用 `asyncio.Lock` 确保并发场景下只初始化一次
- `TurnHandle.stream()` 使用 `acquire_turn_consumer`/`release_turn_consumer` 机制保证同一时间只有一个活跃的 turn 消费者（实验性限制）
- 标记为 `BEGIN GENERATED` / `END GENERATED` 的代码块由 `scripts/update_sdk_artifacts.py` 的 `generate_public_api_flat_methods()` 自动生成
- `Thread.run()` 接受字符串或 `Input`，内部自动规范化为 `TextInput` 后委托给 `turn()` + `_collect_run_result()`
