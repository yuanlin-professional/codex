# `client.py` -- 同步 JSON-RPC 客户端与 app-server 进程管理

## 功能概述

本模块是 SDK 的传输核心，实现了 `AppServerClient` 同步 JSON-RPC 客户端和 `AppServerConfig` 配置类。客户端通过 stdio 管道与 `codex app-server` 子进程通信，支持请求-响应、通知发送与接收、服务端请求处理（如审批处理）等完整的 JSON-RPC 交互模式。模块还包含 codex 二进制文件的定位与解析逻辑。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppServerConfig` | 数据类 | 客户端配置，包含 codex 路径、启动参数、环境变量、客户端标识等 |
| `AppServerClient` | 类 | 同步 JSON-RPC 客户端，管理子进程生命周期和消息读写 |
| `resolve_codex_bin()` | 函数 | 解析 codex 二进制路径：优先使用显式配置，否则从已安装的运行时包中查找 |
| `CodexBinResolverOps` | 数据类 | 二进制解析的可注入操作接口（便于测试） |
| `default_codex_home()` | 函数 | 返回默认的 `~/.codex` 目录路径 |
| `_params_dict()` | 内部函数 | 将 Pydantic 模型或字典统一转换为 JSON-RPC 参数字典 |

## 依赖关系

- **引用的模块**: `.errors`（错误映射）, `.generated.notification_registry`（通知类型注册表）, `.generated.v2_all`（协议类型）, `.models`, `.retry`
- **被引用方**: `.async_client`（异步包装器）, `.api`（高级 API 层）, 测试文件

## 实现备注

- 子进程通过 `subprocess.Popen` 以 stdio 模式启动，stdin/stdout 用于 JSON-RPC 通信，stderr 由后台守护线程持续读取
- `_request_raw()` 内部循环处理消息：自动响应服务端请求（如审批）、缓存通知、匹配响应 ID
- 使用 `threading.Lock` 保护 stdin 写操作的线程安全
- `acquire_turn_consumer`/`release_turn_consumer` 实现了实验性的单活跃消费者约束
- 默认审批处理器自动接受命令执行和文件变更请求
- stderr 使用固定长度的 `deque`（400行）避免内存无限增长
