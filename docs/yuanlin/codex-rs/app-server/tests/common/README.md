# common

## 功能概述

`tests/common/` 是一个独立的 Rust crate（`app-test-support`），为 `app-server` 的集成测试提供公共的测试基础设施和辅助工具。它封装了创建测试环境所需的所有组件，包括 mock 服务器、配置生成、认证模拟、响应构建等。

该模块是所有集成测试的基础依赖，通过提供高层次的抽象来简化测试编写，使测试可以专注于验证业务逻辑而非环境搭建。

## 目录结构

```
common/
├── Cargo.toml                  # crate 配置（名称：app-test-support）
├── BUILD.bazel                 # Bazel 构建配置
├── lib.rs                      # 模块入口，导出所有公共接口
├── mcp_process.rs              # app-server 进程管理客户端
├── mock_model_server.rs        # Mock 模型 API 服务器
├── config.rs                   # 测试配置文件生成
├── responses.rs                # SSE 响应构建辅助
├── auth_fixtures.rs            # ChatGPT 认证模拟夹具
├── analytics_server.rs         # Mock 分析事件服务器
├── models_cache.rs             # 模型缓存文件生成
└── rollout.rs                  # 会话 rollout 文件生成
```

## 核心接口/API

### mcp_process.rs - app-server 进程客户端

- **`McpProcess`** - 封装 app-server 子进程的完整客户端：
  - `new(codex_home)` - 启动 app-server 进程
  - `initialize(params)` - 发送 `initialize` 请求
  - `send_request(method, params)` / `send_notification(method, params)` - 发送 JSON-RPC 请求/通知
  - `thread_start(params)` / `turn_start(params)` / `turn_interrupt(params)` 等 - 各种业务请求的便捷方法
  - `wait_for_response(request_id)` / `wait_for_notification(method)` - 等待响应/通知
  - `wait_for_server_request()` - 等待服务端发起的请求（如命令审批）
  - `wait_for_turn_completed()` - 等待 AI 轮次完成通知
  - 支持约 50+ 种不同的 JSON-RPC 请求方法

- **`DEFAULT_CLIENT_NAME`** - 默认测试客户端名称 `"codex-app-server-tests"`

### mock_model_server.rs - Mock 模型服务器

- **`create_mock_responses_server_sequence(responses)`** - 创建按顺序返回预定义响应的 mock 服务器，验证调用次数
- **`create_mock_responses_server_sequence_unchecked(responses)`** - 同上但不验证调用次数
- **`create_mock_responses_server_repeating_assistant(message)`** - 创建始终返回相同助手消息的 mock 服务器

### config.rs - 配置生成

- **`write_mock_responses_config_toml()`** - 生成指向 mock 服务器的 `config.toml` 文件，支持自定义：
  - 模型提供者 ID 和服务器 URI
  - 功能开关（feature flags）
  - 自动压缩令牌限制
  - 是否要求 OpenAI 认证
  - 压缩提示词

### responses.rs - 响应构建

- **`create_shell_command_sse_response()`** - 构建 shell 命令执行的 SSE 响应
- **`create_final_assistant_message_sse_response()`** - 构建助手最终消息的 SSE 响应
- **`create_apply_patch_sse_response()`** - 构建应用补丁的 SSE 响应
- **`create_exec_command_sse_response()`** - 构建命令执行的 SSE 响应
- **`create_request_user_input_sse_response()`** - 构建用户输入请求的 SSE 响应
- **`create_request_permissions_sse_response()`** - 构建权限请求的 SSE 响应

### auth_fixtures.rs - 认证模拟

- **`ChatGptAuthFixture`** - ChatGPT 认证夹具构建器，支持设置 access token、refresh token、account ID、plan type 等
- **`ChatGptIdTokenClaims`** - JWT ID token 声明构建器
- **`encode_id_token(claims)`** - 将声明编码为 JWT 格式
- **`write_chatgpt_auth(codex_home, fixture, store_mode)`** - 将模拟的认证信息写入 `auth.json`

### analytics_server.rs - 分析服务器

- **`start_analytics_events_server()`** - 启动接受分析事件 POST 请求的 mock 服务器

### models_cache.rs - 模型缓存

- **`write_models_cache(codex_home)`** - 写入包含所有预设模型的缓存文件，避免测试中发起网络请求
- **`write_models_cache_with_models(codex_home, models)`** - 写入指定模型列表的缓存文件

### rollout.rs - 会话 rollout 文件

- **`create_fake_rollout()`** - 创建最小化的 rollout JSONL 文件，用于测试线程列表、线程读取等功能
- **`create_fake_rollout_with_source()`** - 指定 session source 创建 rollout
- **`create_fake_rollout_with_text_elements()`** - 包含文本元素的 rollout
- **`rollout_path(codex_home, filename_ts, thread_id)`** - 计算 rollout 文件路径
