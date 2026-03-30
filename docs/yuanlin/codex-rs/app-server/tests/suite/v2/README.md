# v2

## 功能概述

`tests/suite/v2/` 包含 `app-server` 最全面的集成测试用例集，覆盖了服务器几乎所有的 JSON-RPC 接口。每个测试文件对应一个功能域，通过启动真实的 app-server 进程并模拟客户端交互来验证端到端的正确性。

这些测试使用 `tests/common/` 提供的 `McpProcess` 客户端和 mock 服务器基础设施，验证从初始化到业务操作的完整流程。

## 目录结构

```
v2/
├── mod.rs                                  # 模块入口，声明所有测试子模块
├── initialize.rs                           # 服务器初始化流程测试
├── account.rs                              # 账户认证与管理测试
├── analytics.rs                            # 分析事件上报测试
├── app_list.rs                             # 应用列表查询测试
├── collaboration_mode_list.rs              # 协作模式列表测试
├── command_exec.rs                         # 终端命令执行测试（仅 Unix）
├── compaction.rs                           # 对话压缩测试
├── config_rpc.rs                           # 配置读写 RPC 测试
├── connection_handling_websocket.rs        # WebSocket 连接管理测试
├── connection_handling_websocket_unix.rs   # Unix 特有 WebSocket 测试
├── dynamic_tools.rs                        # 动态工具管理测试
├── experimental_api.rs                     # 实验性 API 测试
├── experimental_feature_list.rs            # 实验性功能列表测试
├── fs.rs                                   # 文件系统操作测试
├── mcp_server_elicitation.rs               # MCP 服务器主动询问测试
├── model_list.rs                           # 模型列表查询测试
├── output_schema.rs                        # 输出 schema 测试
├── plan_item.rs                            # 计划项测试
├── plugin_install.rs                       # 插件安装测试
├── plugin_list.rs                          # 插件列表查询测试
├── plugin_read.rs                          # 插件详情读取测试
├── plugin_uninstall.rs                     # 插件卸载测试
├── rate_limits.rs                          # 速率限制测试
├── realtime_conversation.rs                # 实时对话测试
├── request_permissions.rs                  # 权限请求测试
├── request_user_input.rs                   # 用户输入请求测试
├── review.rs                               # 代码审查测试
├── safety_check_downgrade.rs               # 安全检查降级测试
├── skills_list.rs                          # 技能列表测试
├── thread_archive.rs                       # 线程归档测试
├── thread_fork.rs                          # 线程分叉测试
├── thread_list.rs                          # 线程列表查询测试
├── thread_loaded_list.rs                   # 已加载线程列表测试
├── thread_metadata_update.rs              # 线程元数据更新测试
├── thread_name_websocket.rs                # WebSocket 线程命名测试
├── thread_read.rs                          # 线程内容读取测试
├── thread_resume.rs                        # 线程恢复测试
├── thread_rollback.rs                      # 线程回滚测试
├── thread_shell_command.rs                 # 线程 shell 命令测试
├── thread_start.rs                         # 线程启动测试
├── thread_status.rs                        # 线程状态查询测试
├── thread_unarchive.rs                     # 线程取消归档测试
├── thread_unsubscribe.rs                   # 线程取消订阅测试
├── turn_interrupt.rs                       # AI 轮次中断测试
├── turn_start.rs                           # AI 轮次启动测试
├── turn_start_zsh_fork.rs                  # zsh 分叉轮次启动测试
├── turn_steer.rs                           # AI 轮次引导测试
└── windows_sandbox_setup.rs                # Windows 沙箱设置测试
```

## 核心接口/API

### mod.rs - 模块入口

声明约 50 个测试子模块，其中 `command_exec` 和 `connection_handling_websocket_unix` 仅在 Unix 平台编译（`#[cfg(unix)]`）。

### 主要测试分类

#### 初始化与连接

- **`initialize.rs`** - 测试 `initialize` 请求的参数处理、能力协商、配置警告通知等
- **`connection_handling_websocket.rs`** - 测试 WebSocket 连接的建立、多客户端、健康检查端点等
- **`connection_handling_websocket_unix.rs`** - 测试 Unix 特有的 WebSocket 行为

#### 线程生命周期

- **`thread_start.rs`** - 测试线程创建，包括配置继承、Git 信息注入等
- **`thread_resume.rs`** - 测试从持久化 rollout 文件恢复线程
- **`thread_list.rs`** - 测试线程列表分页查询、过滤和排序
- **`thread_read.rs`** - 测试线程历史内容读取
- **`thread_fork.rs`** - 测试线程分叉（从某个时间点创建新分支）
- **`thread_archive.rs`** / **`thread_unarchive.rs`** - 测试线程归档和取消归档
- **`thread_rollback.rs`** - 测试线程回滚到之前的状态
- **`thread_metadata_update.rs`** - 测试线程元数据（名称等）更新
- **`thread_status.rs`** - 测试线程状态变更通知
- **`thread_unsubscribe.rs`** - 测试线程事件取消订阅
- **`thread_loaded_list.rs`** - 测试当前已加载到内存的线程列表
- **`thread_shell_command.rs`** - 测试线程内 shell 命令执行

#### AI 轮次

- **`turn_start.rs`** - 测试 AI 轮次启动，包括用户消息发送和模型响应处理
- **`turn_interrupt.rs`** - 测试正在进行的 AI 轮次中断
- **`turn_steer.rs`** - 测试 AI 轮次引导（在对话过程中调整方向）
- **`turn_start_zsh_fork.rs`** - 测试 zsh 分叉模式的轮次启动

#### 命令与权限

- **`command_exec.rs`** - 测试终端命令执行、输出流、窗口大小调整和终止
- **`request_permissions.rs`** - 测试权限请求审批流程
- **`request_user_input.rs`** - 测试用户输入请求流程
- **`safety_check_downgrade.rs`** - 测试安全检查降级行为

#### 配置与模型

- **`config_rpc.rs`** - 测试配置读取、写入和批量更新
- **`model_list.rs`** - 测试可用模型列表查询
- **`collaboration_mode_list.rs`** - 测试协作模式预设列表
- **`experimental_api.rs`** / **`experimental_feature_list.rs`** - 测试实验性 API 和功能

#### 插件系统

- **`plugin_install.rs`** - 测试插件安装流程
- **`plugin_list.rs`** - 测试插件列表查询
- **`plugin_read.rs`** - 测试插件详情读取
- **`plugin_uninstall.rs`** - 测试插件卸载

#### 其他功能

- **`fs.rs`** - 测试文件系统操作（读取、写入、复制、删除、目录创建、监控等）
- **`app_list.rs`** - 测试应用连接器列表查询
- **`account.rs`** - 测试账户登录、登出、状态查询
- **`analytics.rs`** - 测试分析事件收集和上报
- **`rate_limits.rs`** - 测试 API 速率限制信息获取
- **`review.rs`** - 测试代码审查功能
- **`compaction.rs`** - 测试对话历史压缩
- **`dynamic_tools.rs`** - 测试动态工具注册和管理
- **`skills_list.rs`** - 测试技能列表查询
- **`output_schema.rs`** - 测试输出 schema 约束
- **`plan_item.rs`** - 测试计划项功能
- **`mcp_server_elicitation.rs`** - 测试 MCP 服务器主动询问机制
- **`realtime_conversation.rs`** - 测试实时对话（音频/文本）功能
- **`windows_sandbox_setup.rs`** - 测试 Windows 沙箱环境配置
