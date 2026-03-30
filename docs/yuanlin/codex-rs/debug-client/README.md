# debug-client

## 功能概述

`codex-debug-client` 是 Codex 项目的调试客户端，提供一个最小化的命令行交互界面，用于与 Codex App Server 进行通信。它通过 JSON-RPC 协议与 `codex` CLI 进程交互，支持创建/恢复会话线程、发送对话消息、管理审批策略等功能。主要面向开发者进行 App Server 协议调试和测试。

## 架构说明

该 crate 是一个纯二进制应用（无 lib.rs），由以下组件构成：

1. **main.rs** - 程序入口，CLI 参数解析（基于 clap）和主交互循环。
2. **client.rs (AppServerClient)** - App Server 客户端，管理与 `codex` CLI 子进程的通信生命周期。
3. **reader.rs** - 异步消息读取器，从 App Server 子进程的标准输出持续读取 JSON-RPC 响应。
4. **commands.rs** - 用户命令解析，将输入文本解析为 `UserCommand` 或消息。
5. **output.rs (Output)** - 输出格式化，管理终端提示符和信息显示。
6. **state.rs** - 状态管理，维护活跃线程和事件通知。

### 交互流程

```
用户输入 -> commands::parse_input -> InputAction::Message / InputAction::Command
                                         |                        |
                                  AppServerClient.send_turn   handle_command
                                         |                        |
                                  JSON-RPC -> codex CLI       :new / :resume / :use / :quit
```

### 支持的命令

| 命令 | 功能 |
|------|------|
| `:help` | 显示帮助信息 |
| `:new` | 创建新会话线程 |
| `:resume <thread-id>` | 恢复已有线程 |
| `:use <thread-id>` | 切换活跃线程 |
| `:refresh-thread` | 列出可用线程 |
| `:quit` | 退出 |
| 其他文本 | 作为消息发送到当前活跃线程 |

## 目录结构

```
debug-client/
  Cargo.toml
  src/
    main.rs       # 程序入口和主循环
    client.rs     # AppServerClient - App Server 通信客户端
    commands.rs   # 用户输入命令解析
    output.rs     # 终端输出格式化
    reader.rs     # JSON-RPC 响应读取器
    state.rs      # 线程状态管理和事件
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `anyhow` | 错误处理 |
| `clap` | 命令行参数解析（derive 模式） |
| `codex-app-server-protocol` | App Server JSON-RPC 协议定义（AskForApproval 等） |
| `serde` / `serde_json` | JSON 序列化/反序列化 |

## 核心接口/API

### CLI 参数

```
codex-debug-client [OPTIONS]

Options:
  --codex-bin <PATH>            codex CLI 路径（默认 "codex"）
  -c, --config <key=value>      转发配置覆盖到 codex CLI（可重复）
  --thread-id <ID>              恢复指定线程
  --approval-policy <POLICY>    审批策略（untrusted/on-failure/on-request/never）
  --auto-approve                自动批准所有审批请求
  --final-only                  仅显示最终助手消息
  --model <MODEL>               模型覆盖
  --model-provider <PROVIDER>   模型提供者覆盖
  --cwd <DIR>                   工作目录覆盖
```

### 审批策略

- `untrusted` / `unless-trusted` - 除信任操作外均需审批
- `on-failure` - 失败时审批
- `on-request` - 按请求审批（默认）
- `never` - 从不审批
