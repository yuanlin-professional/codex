# src

## 功能概述

`src/` 是 `app-server` crate 的主源码目录，包含服务器的所有核心实现代码。该目录组织了从传输层到业务逻辑的完整请求处理流水线，实现了 Codex 应用服务器的全部功能。

服务器架构采用分层设计：
- **传输层**（`transport/`）负责连接管理和协议解析
- **消息处理层**（`message_processor.rs`）负责 JSON-RPC 请求的路由和分发
- **业务逻辑层**（`codex_message_processor.rs`）负责具体的 Codex 功能实现

## 目录结构

```
src/
├── main.rs                         # 可执行入口点
├── lib.rs                          # 库入口，核心事件循环
├── bin/                            # 辅助二进制工具
├── codex_message_processor/        # Codex 消息处理辅助子模块
├── message_processor/              # 消息处理器测试子模块
├── transport/                      # 传输层实现
├── in_process.rs                   # 进程内嵌入运行时
├── message_processor.rs            # 核心消息处理器（请求分发）
├── codex_message_processor.rs      # Codex 业务逻辑处理器
├── outgoing_message.rs             # 出站消息封装与路由定义
├── bespoke_event_handling.rs       # 自定义事件处理（Codex 核心事件转协议事件）
├── config_api.rs                   # 配置读写 API 实现
├── external_agent_config_api.rs    # 外部代理配置 API
├── command_exec.rs                 # 终端命令执行管理器
├── thread_state.rs                 # 会话线程状态管理
├── thread_status.rs                # 线程状态监控和变更通知
├── fs_api.rs                       # 文件系统操作 API（读/写/复制/删除等）
├── fs_watch.rs                     # 文件系统变更监控
├── fuzzy_file_search.rs            # 模糊文件搜索会话管理
├── filters.rs                      # 消息过滤逻辑
├── models.rs                       # 支持的模型列表辅助
├── dynamic_tools.rs                # 动态工具规格管理
├── app_server_tracing.rs           # OpenTelemetry 分布式追踪辅助
├── error_code.rs                   # JSON-RPC 错误码常量定义
└── server_request_error.rs         # 服务端请求错误处理
```

## 核心接口/API

### 入口文件

- **`main.rs`** - 二进制入口，使用 `clap` 解析命令行参数（`--listen`、`--session-source`、`--ws-auth` 等），支持指定传输方式（`stdio://` 或 `ws://IP:PORT`）和会话来源
- **`lib.rs`** - 库入口，定义 `run_main()` 和 `run_main_with_transport()` 两个异步启动函数，包含：
  - 配置加载与校验（含云端需求加载）
  - tracing 日志初始化（支持 JSON 和默认格式）
  - 传输层启动
  - 双任务事件循环（处理器任务 + 出站路由任务）
  - 优雅关闭流程

### 核心处理器

- **`message_processor.rs`** - `MessageProcessor` 结构体，负责：
  - JSON-RPC 请求的路由和分发
  - 连接生命周期管理
  - 外部认证刷新桥接
  - 线程创建监听
  - 后台任务管理

- **`codex_message_processor.rs`** - `CodexMessageProcessor` 结构体，负责所有具体的 Codex 业务逻辑，包括：
  - 线程的创建、恢复、归档、分叉等管理
  - AI 轮次的启动、中断、引导
  - 插件安装/卸载/列表查询
  - 模型列表与协作模式查询
  - 账户认证与管理
  - 代码审查
  - MCP 服务器状态管理

### 进程内运行时

- **`in_process.rs`** - 提供 `InProcessClientHandle`，允许同进程内（如 TUI、exec 模式）直接使用 app-server 功能，无需通过 socket 通信。支持请求/响应、通知推送、背压控制。

### 出站消息系统

- **`outgoing_message.rs`** - 定义 `OutgoingEnvelope`（支持定向发送和广播）、`OutgoingMessageSender`（线程安全的发送器）、`ConnectionId`（连接标识）等核心类型
