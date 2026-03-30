# `mock_responses_websocket_server.py` -- 模拟 Responses API WebSocket 端点的测试服务器

## 功能概述

该脚本启动一个基于 `websockets` 库的本地 WebSocket 服务器，模拟 OpenAI Responses API 的 WebSocket 端点行为。它接受客户端连接后，按照预定义流程先发送一个函数调用事件（`shell_command`），待客户端提交工具输出后再发送一条助手文本消息并关闭连接。该服务器主要用于 `codex-rs` 的集成测试（对应 `agent_websocket.rs` 测试用例），也可作为本地开发调试工具使用。

## 关键函数

| 函数 | 说明 |
|------|------|
| `main()` | 入口函数，解析 `--port` 参数并启动异步事件循环 |
| `_serve(port)` | 绑定 WebSocket 服务器到指定端口，打印配置建议后持续运行 |
| `_handle_connection(websocket)` | 处理单个 WebSocket 连接：验证路径，执行两轮请求-响应交互 |
| `_event_response_created(response_id)` | 构造 `response.created` 事件 |
| `_event_response_done()` | 构造 `response.done` 事件（含 usage 统计） |
| `_event_response_completed(response_id)` | 构造 `response.completed` 事件 |
| `_event_function_call(call_id, name, arguments_json)` | 构造函数调用事件（`response.output_item.done`） |
| `_event_assistant_message(message_id, text)` | 构造助手文本消息事件 |
| `_dump_json(payload)` | 将 Python 对象序列化为紧凑 JSON 字符串 |
| `_print_request(prefix, payload)` | 格式化打印收到的请求 JSON（带 UTC 时间戳） |

## 使用方式

```bash
# 在默认端口 8765 启动
python scripts/mock_responses_websocket_server.py

# 使用随机可用端口启动
python scripts/mock_responses_websocket_server.py --port 0
```

启动后会打印 `config.toml` 配置片段，提示用户如何配置 codex 使用此模拟服务器。

## 实现备注

- 交互流程为两轮：第一轮收到请求后返回 `shell_command` 函数调用；第二轮收到工具输出后返回助手文本消息 `"done"` 并关闭连接。
- WebSocket 路径限定为 `/v1/responses`，非预期路径会以 1008 状态码拒绝。
- 兼容 `websockets` v15 的新接口（通过 `websocket.request.path` 获取路径）。
- 所有收发消息均带 UTC 时间戳打印到标准输出，便于调试。
