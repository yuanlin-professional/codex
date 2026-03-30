# `thread_start.rs` — 测试模块

## 测试覆盖范围
测试 `thread/start` JSON-RPC 方法，验证线程创建和 thread/started 通知、项目配置遵守、
Flex 服务层级、指标服务名称、临时线程无路径、MCP 服务器初始化失败处理、
MCP 服务器状态更新通知，以及云需求加载错误。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_start_creates_thread_and_emits_started` | 创建线程并发送 thread/started 通知 |
| `thread_start_respects_project_config_from_cwd` | 遵守 cwd 对应的项目配置 |
| `thread_start_accepts_flex_service_tier` | 接受 Flex 服务层级参数 |
| `thread_start_accepts_metrics_service_name` | 接受指标服务名称参数 |
| `thread_start_ephemeral_remains_pathless` | 临时线程保持无路径 |
| `thread_start_fails_when_required_mcp_server_fails_to_initialize` | 必需的 MCP 服务器初始化失败时启动失败 |
| `thread_start_emits_mcp_server_status_updated_notifications` | 发送 MCP 服务器状态更新通知 |
| `thread_start_surfaces_cloud_requirements_load_errors` | 云需求加载错误被表面化 |
