# codex-rs/core 架构文档

## 1. 概述

`codex-core` 是 Codex AI 助手的中央运行时 crate，负责管理完整的 AI 会话生命周期（线程创建、回合执行、历史持久化）、编排工具执行流水线（审批 → 沙箱 → 执行 → 重试）、构建基于片段的上下文注入系统、集成 Guardian 安全审查断路器，并支持多 Agent 信箱协调。它是连接上层 UI（TUI/App-Server/Exec CLI）与底层服务 crate（模型 API、沙箱、MCP、持久化）的枢纽。

规模：~200+ 源文件、~50,000 行代码。

---

## 2. 功能模块索引

| 模块标签 | 职责一句话 | 包含文件/目录 |
|----------|-----------|--------------|
| `[会话管理]` | 管理线程生命周期与会话运行时调度循环 | `thread_manager.rs`, `codex_thread.rs`, `session/`, `state/`, `tasks/` |
| `[模型通信]` | 与模型 API 进行认证、连接管理和流式通信 | `client.rs`, `client_common.rs`, `realtime_*.rs`, `attestation.rs`, `session_startup_prewarm.rs` |
| `[工具系统]` | 工具注册、路由、审批编排、沙箱选择、并行执行 | `tools/` |
| `[安全审批]` | 执行策略匹配、Guardian 审查、沙箱类型判定 | `guardian/`, `safety.rs`, `exec_policy.rs`, `sandbox_tags.rs`, `sandboxing/` |
| `[上下文构建]` | 管理 30+ 上下文片段注入与历史 token 截断 | `context/`, `context_manager/`, `compact.rs`, `compact_remote*.rs` |
| `[进程执行]` | 子进程产生（PTY/pipe）、输出收集、进程池、沙箱包装 | `unified_exec/`, `exec.rs`, `exec_env.rs`, `spawn.rs`, `shell*.rs`, `landlock.rs`, `windows_sandbox*.rs` |
| `[配置系统]` | 多层配置加载、权限编译、Feature flag、Schema 生成 | `config/`, `config_lock.rs` |
| `[持久化]` | 事件流写入磁盘、SQLite 状态数据库、历史恢复 | `rollout.rs`, `state_db_bridge.rs`, `thread_rollout_truncation.rs` |
| `[多Agent]` | 子 Agent 产生、信箱通信、状态追踪、深度限制 | `agent/`, `codex_delegate.rs`, `agents_md.rs` |
| `[插件MCP]` | MCP 服务器管理、工具调用路由、插件发现、技能管理 | `mcp*.rs`, `plugins/`, `skills.rs`, `connectors.rs`, `apps/` |
| `[Hook事件]` | 生命周期钩子执行与内部状态到外部事件流映射 | `hook_runtime.rs`, `event_mapping.rs`, `stream_events_utils.rs`, `turn_metadata.rs`, `turn_timing.rs`, `turn_diff_tracker.rs` |
| `[辅助]` | 通用工具函数、命令规范化、网络策略等零散功能 | `util.rs`, `utils/`, `goals.rs`, `web_search.rs`, `mention_syntax.rs` 等 |

---

## 3. 逐目录逐文件说明

### 3.1 顶层文件 (src/*.rs)

顶层文件承载 crate 入口、跨模块的单职责组件以及不适合归入子目录的独立功能。

**agents_md.rs** `[多Agent]` — 管理项目中的 `agents.md` 文件，定义自定义 Agent 角色。

**apply_patch.rs** `[工具系统]` — Patch 应用逻辑的本地桥接/包装。

**attestation.rs** `[模型通信]` — 外部 attestation 提供方集成，用于请求头注入。
- `AttestationContext`、`AttestationProvider`、`GenerateAttestationFuture` 类型

**client.rs** `[模型通信]` — 会话作用域的模型 API 客户端，管理认证、WebSocket 连接池和流式请求。
- `ModelClient` struct：持有认证 provider、连接配置、prewarm handle，每个 Session 一个实例
- `ModelClientSession` struct：每个回合一个实例，封装到模型 API 的一次流式请求，返回 `ResponseStream`
- WebSocket prewarm 逻辑：在回合间预建连接减少首字节延迟
- 传输降级：WebSocket → SSE 自动切换
- 常量 `X_CODEX_INSTALLATION_ID_HEADER`、`X_CODEX_TURN_METADATA_HEADER`

**client_common.rs** `[模型通信]` — 模型通信层的共享类型定义。
- `Prompt` struct：封装发送给模型的完整请求体（history items + tool definitions + instructions）
- `ResponseStream` struct：异步流，yield `ResponseEvent`
- 常量 `REVIEW_PROMPT`、`REVIEW_EXIT_SUCCESS_TMPL`、`REVIEW_EXIT_INTERRUPTED_TMPL`

**codex_delegate.rs** `[多Agent]` — 委托模式，以交互方式运行子 Agent 线程。
- `run_codex_thread_interactive()` async fn

**codex_thread.rs** `[会话管理]` — 对外暴露的线程控制接口，用户通过此类型提交操作和接收事件。
- `CodexThread` struct：封装 Op sender + Event receiver 通道，提供 `submit(op)` / `shutdown_and_wait()` / `wait_until_terminated()` 方法
- `ThreadConfigSnapshot` struct：线程创建时的配置快照（model、cwd、permission_profile 等）
- `CodexThreadSettingsOverrides` struct：运行时可修改的设置覆盖（model、approval_policy 等）

**command_canonicalization.rs** `[辅助]` — 命令规范化（将 alias 等转换为标准形式）。

**compact.rs** `[上下文构建]` — 本地历史压缩，当 token 使用超限时调用摘要模型缩减历史。
- `InitialContextInjection` enum：`BeforeLastUserMessage` / `DoNotInject`
- `run_inline_auto_compact_task()` async fn：自动触发压缩
- `run_compact_task()` async fn：手动压缩
- `content_items_to_text()` fn

**compact_remote.rs** `[上下文构建]` — 远端压缩 v1，通过远程 API 执行历史摘要。

**compact_remote_v2.rs** `[上下文构建]` — 远端压缩 v2，改进的远程压缩协议。

**config_lock.rs** `[配置系统]` — 配置锁文件处理。

**connectors.rs** `[插件MCP]` — App 连接器集成，管理第三方应用（如 Linear、GitHub）的工具和品牌信息。
- `AppBranding`、`AppInfo`、`AppMetadata` struct（re-export）
- `AppToolPolicy` struct

**environment_selection.rs** `[工具系统]` — 环境选择逻辑，决定工具在哪个执行环境中运行。

**event_mapping.rs** `[Hook事件]` — 将模型 API 返回的 `ResponseItem` 转换为面向用户的 `TurnItem` 事件。
- `is_contextual_user_message_content()` fn
- `is_contextual_dev_message_content()` fn
- `parse_turn_item()` fn

**exec.rs** `[进程执行]` — Shell 工具执行的核心入口，将工具调用转换为受沙箱保护的子进程执行。
- `ExecParams` struct：执行参数（command、cwd、timeout、capture_policy、env、sandbox_permissions）
- `ExecExpiration` enum：`Timeout(Duration)` / `DefaultTimeout` / `Cancellation(CancellationToken)` / `TimeoutOrCancellation`
- `ExecExpirationOutcome` enum：`TimedOut` / `Cancelled`
- `ExecCapturePolicy` enum：`ShellTool`（head+tail 截断） / `FullBuffer`（完整捕获）
- `WindowsSandboxFilesystemOverrides` struct：Windows 沙箱文件系统覆盖
- `StdoutStream` struct：stdout 事件流
- `process_exec_tool_call()` async fn：Shell 工具主入口
- `build_exec_request()` fn / `execute_exec_request()` async fn

**exec_env.rs** `[进程执行]` — 为子进程构建环境变量映射，注入 Codex 专有变量（thread_id、sandbox 标识等）。
- `create_env()` fn：根据 ExecServerEnvPolicy 和 session 状态构建完整 env map
- 常量 `CODEX_THREAD_ID_ENV_VAR`

**exec_policy.rs** `[安全审批]` — 声明式执行策略引擎，根据 ExecPolicy 规则判定命令是否允许/拒绝/需审批。
- 与外部 `codex-execpolicy` crate 集成
- `load_exec_policy()` fn、`check_execpolicy_for_warnings()` fn
- `ExecPolicyError` 类型

**function_tool.rs** `[工具系统]` — 工具错误类型 re-export（`FunctionCallError` from `codex-tools`）。

**goals.rs** `[辅助]` — 目标追踪系统，管理会话中的分层目标（创建、更新、查询）。
- `SetGoalRequest` struct、`CreateGoalRequest` struct
- `ExternalGoalSet`、`ExternalGoalPreviousStatus` 类型
- 模板常量：`CONTINUATION_PROMPT_TEMPLATE` 等

**hook_runtime.rs** `[Hook事件]` — Hook 执行运行时，在关键生命周期节点运行用户配置的 Shell 命令。
- 支持的 hook 点：`session_start`、`user_prompt_submit`、`pre_tool_use`、`post_tool_use`、`permission_request`、`turn_stop`、`pre_compact`、`post_compact`
- `HookRuntimeOutcome` struct：hook 执行结果
- `PreToolUseHookResult` enum：`Continue` / `Blocked(reason)`
- `run_pending_session_start_hooks()` async fn 等

**installation_id.rs** `[辅助]` — 安装 ID 管理（持久化唯一标识）。

**landlock.rs** `[进程执行]` — Linux Landlock LSM 沙箱集成。
- `spawn_command_under_linux_sandbox()` fn

**lib.rs** `[会话管理]` — crate 根入口，声明所有子模块并通过 `pub use` 导出公共 API 表面。
- 模块声明：约 60 个 `mod` 声明覆盖所有子模块
- 公共 re-exports：`CodexThread`、`ThreadManager`、`ModelClient`、`Config`、`RolloutRecorder`、`McpManager`、`SkillsManager` 等核心类型
- 条件编译：`#[cfg(test)]` 下的 `test_support` 模块

**mcp.rs** `[插件MCP]` — MCP 服务器连接管理器。
- `McpManager` struct：管理 MCP 服务器生命周期、连接池和工具发现
- 方法：`new()`、`configured_servers()`、`effective_servers()`、`tool_plugin_provenance()`

**mcp_openai_file.rs** `[插件MCP]` — 处理 MCP 上下文中的 OpenAI 文件引用。

**mcp_skill_dependencies.rs** `[插件MCP]` — MCP 技能依赖解析。

**mcp_tool_approval_templates.rs** `[插件MCP]` — MCP 工具审批模板匹配，根据模板缓存审批决定。

**mcp_tool_call.rs** `[插件MCP]` — MCP 工具调用执行逻辑，处理参数解析、审批检查、调用路由和输出格式化/截断。

**mcp_tool_exposure.rs** `[插件MCP]` — MCP 工具暴露配置，控制哪些 MCP 工具对模型可见。

**memory_usage.rs** `[辅助]` — 内存使用追踪。

**mention_syntax.rs** `[辅助]` — @mention 语法解析。

**network_policy_decision.rs** `[安全审批]` — 网络访问策略决策逻辑。

**network_proxy_loader.rs** `[配置系统]` — 网络代理配置加载与热重载。
- `build_network_proxy_state()` fn

**original_image_detail.rs** `[辅助]` — 图片细节级别追踪。

**otel_init.rs** `[辅助]` — OpenTelemetry 初始化配置。

**personality_migration.rs** `[辅助]` — 人格配置迁移。

**prompt_debug.rs** `[辅助]` — Prompt 调试工具。

**realtime_context.rs** `[模型通信]` — 构建 Realtime 会话启动所需的上下文信息。

**realtime_conversation.rs** `[模型通信]` — Realtime API 会话管理，处理实时语音/视频交互。
- `RealtimeConversationEnd` enum：`Requested` / `TransportClosed`

**realtime_prompt.rs** `[模型通信]` — Realtime 会话的 prompt 模板。

**review_format.rs** `[辅助]` — 审查模式格式化。

**review_prompts.rs** `[辅助]` — 审查模式 prompt 模板。

**rollout.rs** `[持久化]` — 持久化桥接层，re-export `codex-rollout` crate 的核心类型。
- `RolloutRecorder` struct：事件记录器
- `RolloutRecorderParams` struct：录制参数
- `Cursor`、`ThreadItem`、`ThreadsPage`、`SessionMeta` 等查询类型
- 线程/会话路径查找函数

**safety.rs** `[安全审批]` — Patch 安全性评估，判定文件写入是否在允许路径范围内。
- `SafetyCheck` enum：`AutoApprove` / `AskUser` / `Reject`
- `assess_patch_safety()` fn

**sandbox_tags.rs** `[安全审批]` — 根据权限配置计算沙箱类型标签，用于 telemetry。
- `permission_profile_sandbox_tag()` fn
- `permission_profile_policy_tag()` fn

**session_prefix.rs** `[辅助]` — 会话前缀处理。

**session_rollout_init_error.rs** `[持久化]` — 会话 rollout 初始化错误处理。

**session_startup_prewarm.rs** `[模型通信]` — 会话启动时的 WebSocket 连接预热逻辑。

**shell.rs** `[进程执行]` — Shell 类型推导与命令包装（将 user command string 包装为平台 shell 调用）。

**shell_detect.rs** `[进程执行]` — 检测当前系统使用的 Shell（bash/zsh/fish/powershell 等）。

**shell_snapshot.rs** `[进程执行]` — 捕获当前 Shell 环境状态（env vars、aliases 等）供后续恢复。

**skills.rs** `[插件MCP]` — 技能管理入口，re-export `codex-core-skills` crate。
- `SkillsManager` struct：技能注册与生命周期
- `SkillMetadata` struct
- `build_available_skills()` fn、`build_skill_injections()` fn
- `maybe_emit_implicit_skill_invocation()` async fn

**spawn.rs** `[进程执行]` — 子进程产生层，处理沙箱命令包装、网络禁用、PTY 分配。
- `SpawnChildRequest` struct：完整的产生请求（command、env、cwd、sandbox、stdio_policy）
- `StdioPolicy` enum：`RedirectForShellTool` / `Inherit`
- `spawn_child_async()` async fn：平台无关的子进程产生入口
- 常量 `CODEX_SANDBOX_NETWORK_DISABLED_ENV_VAR`、`CODEX_SANDBOX_ENV_VAR`

**state_db_bridge.rs** `[持久化]` — SQLite 状态数据库初始化桥接。
- `StateDbHandle` struct（re-export）
- `init_state_db()` async fn

**stream_events_utils.rs** `[Hook事件]` — 回合流式事件处理工具，管理 tool call 批次执行、输出格式化、图片生成等。
- 回合主循环逻辑：接收 ResponseEvent → 分发 tool calls → 收集结果 → 继续
- `image_generation_artifact_path()` fn
- `ToolCallRuntime` 集成

**test_support.rs** `[辅助]` — 测试基础设施（仅 `#[cfg(test)]`）。

**thread_manager.rs** `[会话管理]` — 管理所有 Codex 线程的创建、恢复、fork 和关闭，是最上层的入口协调器。
- `ThreadManager` struct：持有 thread store、models manager、MCP manager、analytics 等共享资源，提供 `start_thread()`、`fork_thread()`、`shutdown()` 等方法
- `NewThread` struct：封装新创建线程的 thread_id + `CodexThread` handle + 首个配置事件
- `StartThreadOptions` struct：线程启动可选参数（history、config overrides、session mode 等）
- `ForkSnapshot` enum：`TruncateBeforeNthUserMessage(usize)` / `Interrupted` — 指定 fork 时的历史截断策略
- `ThreadShutdownReport` struct：汇总关闭后的状态信息
- 工厂函数 `build_models_manager()`、`thread_store_from_config()`

**thread_rollout_truncation.rs** `[持久化]` — 线程历史截断策略。

**turn_diff_tracker.rs** `[Hook事件]` — 回合状态差异追踪，用于增量上下文更新。

**turn_metadata.rs** `[Hook事件]` — 回合元数据（model、timing、token usage 等）。

**turn_timing.rs** `[Hook事件]` — 回合计时信息。

**user_shell_command.rs** `[辅助]` — 用户 Shell 命令处理。

**util.rs** `[辅助]` — 通用工具函数。

**web_search.rs** `[辅助]` — Web 搜索功能集成。

**windows_sandbox.rs** `[进程执行]` — Windows 沙箱配置与命令包装。

**windows_sandbox_read_grants.rs** `[进程执行]` — Windows 沙箱读权限路径授予。

---

### 3.2 agent/

多 Agent 协调，管理子 Agent 的产生、通信和生命周期。

**agent/agent_resolver.rs** `[多Agent]` — Agent 服务解析。

**agent/control.rs** `[多Agent]` — Agent 编排控制。
- `AgentControl` struct：管理子 Agent 集合，提供 spawn/message/wait/close 操作
- `SpawnAgentOptions` struct / `LiveAgent` struct / `ListedAgent` struct
- `SpawnAgentForkMode` enum：`FullHistory` / `LastNTurns(usize)`

**agent/mod.rs** `[多Agent]` — 模块入口。
- `AgentStatus` enum：`Spawned` / `Running` / `Waiting` / `Completed` / `Failed` / `Terminated`
- `exceeds_thread_spawn_depth_limit()` / `next_thread_spawn_depth()` fn

**agent/registry.rs** `[多Agent]` — Agent 注册与并发限制。

**agent/role.rs** `[多Agent]` — Agent 角色定义与配置应用。

**agent/status.rs** `[多Agent]` — Agent 状态追踪。

---

### 3.3 apps/

App 连接器支持。

**apps/mod.rs** `[插件MCP]` — 模块入口。

**apps/render.rs** `[插件MCP]` — App 信息渲染。

---

### 3.4 config/

配置系统，负责多层配置加载、权限编译、Feature flag 管理。

**config/agent_roles.rs** `[多Agent]` — Agent 角色定义（从 agents.md 解析）。

**config/edit.rs** `[配置系统]` — 配置文件编辑支持（运行时修改 config 文件）。

**config/managed_features.rs** `[配置系统]` — Feature flag 管理。

**config/mod.rs** `[配置系统]` — 配置模块入口，re-export `Config` 及加载逻辑。
- `Config`（from `codex-config`）
- `Constrained`、`ConstraintError`、`ConstraintResult` — 约束验证类型
- `LoaderOverrides`、`ConfigLoadOptions` — 加载选项

**config/network_proxy_spec.rs** `[配置系统]` — 网络代理规格定义与验证。

**config/otel.rs** `[配置系统]` — OpenTelemetry 相关配置。

**config/permissions.rs** `[配置系统]` — 权限配置编译，将声明式权限定义编译为运行时可用的权限对象。

**config/resolved_permission_profile.rs** `[配置系统]` — 权限配置最终解析结果。

**config/schema.rs** `[配置系统]` — 配置 JSON Schema 自动生成。

---

### 3.5 context/

上下文片段定义，每个文件实现一个可注入 system prompt 的上下文片段。

**context/approved_command_prefix_saved.rs** `[上下文构建]` — 命令前缀审批通知片段。

**context/apps_instructions.rs** `[上下文构建]` — App 连接器信息片段。

**context/available_plugins_instructions.rs** `[上下文构建]` — 可用插件列表片段。

**context/available_skills_instructions.rs** `[上下文构建]` — 可用技能列表片段。

**context/collaboration_mode_instructions.rs** `[上下文构建]` — 协作模式配置片段。

**context/contextual_user_message.rs** `[上下文构建]` — 用户消息上下文处理。

**context/environment_context.rs** `[上下文构建]` — 环境信息片段（OS、shell、cwd、git info 等）。

**context/fragment.rs** `[上下文构建]` — 上下文片段核心 trait。
- `ContextualUserFragment` trait：定义 `description()` / `as_string()` / `enabled()` 方法
- `FragmentRegistration` trait：片段注册接口
- `FragmentRegistrationProxy<T>` struct：类型擦除代理

**context/goal_context.rs** `[上下文构建]` — 当前目标状态片段。

**context/guardian_followup_review_reminder.rs** `[上下文构建]` — Guardian 审查跟进提醒片段。

**context/hook_additional_context.rs** `[上下文构建]` — Hook 附加上下文片段。

**context/image_generation_instructions.rs** `[上下文构建]` — 图片生成指令片段。

**context/legacy_apply_patch_exec_command_warning.rs** `[上下文构建]` — 旧版 patch/exec 警告片段。

**context/legacy_model_mismatch_warning.rs** `[上下文构建]` — 模型不匹配警告片段。

**context/legacy_unified_exec_process_limit_warning.rs** `[上下文构建]` — 进程限制警告片段。

**context/mod.rs** `[上下文构建]` — 片段注册入口，导出所有片段类型和辅助函数。

**context/model_switch_instructions.rs** `[上下文构建]` — 模型切换指导片段。

**context/network_rule_saved.rs** `[上下文构建]` — 网络规则保存通知片段。

**context/permissions_instructions.rs** `[上下文构建]` — 权限规则注入片段，告知模型当前权限约束。

**context/personality_spec_instructions.rs** `[上下文构建]` — 人格规格片段。

**context/plugin_instructions.rs** `[上下文构建]` — 单个插件详细指令片段。

**context/realtime_end_instructions.rs** `[上下文构建]` — Realtime 会话结束片段。

**context/realtime_start_instructions.rs** `[上下文构建]` — Realtime 会话开始片段。

**context/realtime_start_with_instructions.rs** `[上下文构建]` — Realtime 带指令启动片段。

**context/skill_instructions.rs** `[上下文构建]` — 单个技能详细指令片段。

**context/subagent_notification.rs** `[上下文构建]` — 子 Agent 通知片段。

**context/turn_aborted.rs** `[上下文构建]` — 回合中断通知片段。

**context/user_instructions.rs** `[上下文构建]` — 用户自定义指令片段。

**context/user_shell_command.rs** `[上下文构建]` — 用户 Shell 命令上下文片段。

---

### 3.6 context_manager/

上下文历史管理，维护 history items 列表、token 使用统计和压缩窗口。

**context_manager/history.rs** `[上下文构建]` — 上下文管理器核心实现。
- `ContextManager` struct：维护 history items 列表、token 使用统计、压缩窗口
- `TotalTokenUsageBreakdown` struct
- `estimate_response_item_model_visible_bytes()` fn
- `is_user_turn_boundary()` fn
- `truncate_function_output_payload()` fn

**context_manager/mod.rs** `[上下文构建]` — 模块入口，导出 `ContextManager` 等。

**context_manager/normalize.rs** `[上下文构建]` — 历史归一化，合并/清理冗余项。

**context_manager/updates.rs** `[上下文构建]` — 增量上下文更新逻辑。

---

### 3.7 guardian/

Guardian 安全审查子系统，通过独立模型会话评估工具执行的风险级别。

**guardian/approval_request.rs** `[安全审批]` — 审查请求构造。

**guardian/mod.rs** `[安全审批]` — 模块入口与核心类型。
- `GuardianApprovalRequest` struct
- `GuardianAssessment` struct：结构化评估结果（risk_level、user_authorization、outcome、rationale）
- `GuardianRejection` struct / `GuardianRejectionCircuitBreaker` struct
- `GuardianRejectionCircuitBreakerAction` enum：`Continue` / `InterruptTurn`
- 常量 `GUARDIAN_REVIEW_TIMEOUT`（90s）、`MAX_CONSECUTIVE_GUARDIAN_DENIALS_PER_TURN`（3）
- `routes_approval_to_guardian()` fn / `review_approval_request()` async fn

**guardian/prompt.rs** `[安全审批]` — Guardian 审查 prompt 构建。

**guardian/review.rs** `[安全审批]` — 审查执行逻辑。

**guardian/review_session.rs** `[安全审批]` — Guardian 审查会话管理（独立模型会话）。

---

### 3.8 plugins/

插件框架，负责插件发现、上下文注入和渲染。

**plugins/discoverable.rs** `[插件MCP]` — 插件发现逻辑，搜索可安装插件。
- `list_tool_suggest_discoverable_plugins()` fn

**plugins/injection.rs** `[插件MCP]` — 插件上下文注入。
- `build_plugin_injections()` fn

**plugins/mentions.rs** `[插件MCP]` — 插件 @mention 解析。
- `collect_explicit_plugin_mentions()` fn
- `collect_tool_mentions_from_messages()` fn
- `build_connector_slug_counts()` fn

**plugins/mod.rs** `[插件MCP]` — 模块入口，导出插件发现、注入、渲染函数。

**plugins/render.rs** `[插件MCP]` — 插件信息渲染为文本。
- `render_explicit_plugin_instructions()` fn

---

### 3.9 sandboxing/

沙箱适配层，将高层执行请求转换为平台特定沙箱命令。

**sandboxing/mod.rs** `[安全审批]` — 沙箱请求/响应适配。
- `SandboxState` struct（re-export）

---

### 3.10 session/

会话运行时核心，实现 Session 主循环和回合调度。是「线程」概念在内部的实际执行引擎。

**session/config_lock.rs** `[配置系统]` — 会话级配置锁。

**session/handlers.rs** `[会话管理]` — Op 消息处理器，根据 Op 类型分发到不同处理路径。
- Submit → 启动 Regular/Review 任务
- Approve → 转发审批决定
- Interrupt → 取消当前回合
- Compact → 启动压缩任务
- InterAgent → 信箱投递
- Configure → 更新会话设置
- Shutdown → 清理并退出

**session/input_queue.rs** `[会话管理]` — 输入队列/信箱，管理待处理的 Op 消息，支持优先级和 inter-agent 投递。

**session/mcp.rs** `[插件MCP]` — 会话级 MCP 连接管理集成。

**session/mod.rs** `[会话管理]` — 模块入口，导出 `SteerInputError`、`TurnContext` 等类型。

**session/multi_agents.rs** `[多Agent]` — 会话级多 Agent 协调逻辑。

**session/review.rs** `[辅助]` — 审查模式处理。

**session/rollout_reconstruction.rs** `[持久化]` — 从 rollout 文件重建会话状态。

**session/session.rs** `[会话管理]` — Session 结构体定义及其核心方法。
- `Session` struct：已初始化的 AI Agent 运行时上下文，持有 config、services、context_manager、approval_store 等
- `SessionConfiguration` struct：会话配置包装
- 核心方法：`handle_operation()`（主 Op 调度）、`build_turn_context()`、`get_visible_tool_specs()`

**session/turn.rs** `[会话管理]` — 回合状态机与执行逻辑，管理回合生命周期及工具调用循环。

**session/turn_context.rs** `[会话管理]` — 每回合执行上下文，携带回合级配置。
- `TurnContext` struct：sub_id、model_info、cwd、approval_policy、permission_profile、environment 等

---

### 3.11 state/

会话状态管理，聚合会话作用域的所有服务和回合级状态。

**state/auto_compact_window.rs** `[上下文构建]` — 自动压缩窗口追踪。

**state/mod.rs** `[会话管理]` — 模块入口。

**state/service.rs** `[会话管理]` — SessionServices 聚合器。
- `SessionServices` struct：聚合 MCP 连接管理器、unified exec 管理器、analytics client、hook runtime、telemetry、tool approvals、network proxy、model client、auth manager 等

**state/session.rs** `[会话管理]` — 会话级状态。
- `SessionState` struct：持久化会话元数据

**state/turn.rs** `[会话管理]` — 回合级状态。
- `ActiveTurn` struct / `TurnState` enum

---

### 3.12 tasks/

回合任务分发，根据不同的操作类型启动相应的回合任务。

**tasks/compact.rs** `[上下文构建]` — 压缩回合任务。

**tasks/lifecycle.rs** `[Hook事件]` — 任务生命周期事件。

**tasks/mod.rs** `[会话管理]` — 任务分发入口。

**tasks/regular.rs** `[会话管理]` — 常规回合任务（用户输入 → 模型响应 → 工具执行循环）。

**tasks/review.rs** `[辅助]` — 审查回合任务。

**tasks/user_shell.rs** `[辅助]` — 用户 Shell 任务。

---

### 3.13 tools/

工具执行框架，实现完整的工具生命周期——注册、路由、审批、沙箱、执行、重试。

**tools/context.rs** `[工具系统]` — ToolEventCtx，工具执行时的事件上下文。

**tools/events.rs** `[Hook事件]` — 工具事件发射器。

**tools/hook_names.rs** `[Hook事件]` — Hook 名称常量。

**tools/hosted_spec.rs** `[工具系统]` — 托管工具（hosted tools）规格定义。

**tools/lifecycle.rs** `[Hook事件]` — 工具生命周期事件发射（开始/结束/错误）。

**tools/mod.rs** `[工具系统]` — 框架入口，导出核心类型和输出格式化函数。
- `format_exec_output_for_model()` fn
- `format_exec_output_str()` fn
- 常量 `TELEMETRY_PREVIEW_MAX_BYTES`、`TELEMETRY_PREVIEW_MAX_LINES`

**tools/network_approval.rs** `[安全审批]` — 网络访问审批逻辑。

**tools/orchestrator.rs** `[工具系统]` — 审批 + 沙箱 + 执行 + 重试编排器。
- `ToolOrchestrator` struct：统一编排所有工具的执行流水线
- `OrchestratorRunResult<Out>` struct
- 流程：判定审批需求 → 请求审批（缓存/Guardian/用户） → 选择沙箱 → 执行 → 沙箱拒绝时降级重试

**tools/parallel.rs** `[工具系统]` — 并行 tool call 批次执行。
- `ToolCallRuntime`：spawn 多个 tool call 任务并 `join_all()` 收集结果

**tools/registry.rs** `[工具系统]` — 工具注册表与执行 trait。
- `ToolRegistry` struct：维护所有已注册工具（内置 + MCP + 扩展 + 动态）
- `CoreToolRuntime` trait：内置工具执行合约，定义 `run()` 方法
- `ToolArgumentDiffConsumer` trait：流式参数差异消费者
- `AnyToolResult` struct / `PreToolUsePayload` / `PostToolUsePayload` struct

**tools/router.rs** `[工具系统]` — 工具路由与分发。
- `ToolRouter` struct：持有 ToolRegistry + 路由规则
- 方法：`from_turn_context()`、`model_visible_specs()`、`tool_supports_parallel()`、`build_tool_call()`

**tools/sandboxing.rs** `[安全审批]` — 沙箱策略计算，根据权限配置和工具类型决定沙箱级别。

**tools/spec_plan.rs** `[工具系统]` — 工具规格可见性规划。

**tools/tool_dispatch_trace.rs** `[工具系统]` — 工具调度追踪信息。

**tools/tool_search_entry.rs** `[工具系统]` — 工具搜索条目。

---

### 3.14 tools/code_mode/

代码执行模式。

**tools/code_mode/execute_handler.rs** `[工具系统]` — execute 工具 handler。

**tools/code_mode/execute_spec.rs** `[工具系统]` — execute 工具规格。

**tools/code_mode/mod.rs** `[工具系统]` — 模块入口。

**tools/code_mode/response_adapter.rs** `[工具系统]` — 响应适配。

**tools/code_mode/wait_handler.rs** `[工具系统]` — wait 工具 handler。

**tools/code_mode/wait_spec.rs** `[工具系统]` — wait 工具规格。

---

### 3.15 tools/handlers/

具体工具实现，每个工具通常有 handler（执行逻辑）+ spec（工具规格/JSON Schema）。

**handlers/agent_jobs.rs** + **handlers/agent_jobs_spec.rs** `[多Agent]` — Agent Job 管理工具。
- `agent_jobs/spawn_agents_on_csv.rs` — 批量产生 Agent
- `agent_jobs/report_agent_job_result.rs` — 汇报结果

**handlers/apply_patch.rs** + **handlers/apply_patch_spec.rs** `[工具系统]` — 文件 Patch 应用工具。

**handlers/dynamic.rs** `[工具系统]` — 动态工具生成。

**handlers/extension_tools.rs** `[工具系统]` — 扩展工具 handler。

**handlers/goal.rs** + **handlers/goal_spec.rs** `[工具系统]` — 目标管理工具。
- `goal/create_goal.rs`、`get_goal.rs`、`update_goal.rs`

**handlers/list_available_plugins_to_install.rs** + spec `[插件MCP]` — 列出可安装插件工具。

**handlers/mcp.rs** `[插件MCP]` — MCP 工具调用 handler。

**handlers/mcp_resource.rs** + **handlers/mcp_resource_spec.rs** `[插件MCP]` — MCP 资源操作工具。
- `mcp_resource/list_mcp_resources.rs`、`list_mcp_resource_templates.rs`、`read_mcp_resource.rs`

**handlers/mod.rs** `[工具系统]` — 所有 handler 的注册入口。

**handlers/multi_agents.rs** + **handlers/multi_agents_spec.rs** `[多Agent]` — 多 Agent v1（spawn/send_input/wait/close/resume）。
- `multi_agents/spawn.rs`、`send_input.rs`、`wait.rs`、`close_agent.rs`、`resume_agent.rs`

**handlers/multi_agents_common.rs** `[多Agent]` — 两版多 Agent 共享逻辑。

**handlers/multi_agents_v2.rs** `[多Agent]` — 多 Agent v2（spawn/send_message/followup/wait/list/close）。
- `multi_agents_v2/spawn.rs`、`send_message.rs`、`followup_task.rs`、`wait.rs`、`list_agents.rs`、`close_agent.rs`、`message_tool.rs`

**handlers/plan.rs** + **handlers/plan_spec.rs** `[工具系统]` — 计划工具。

**handlers/request_permissions.rs** `[安全审批]` — 权限请求工具。

**handlers/request_plugin_install.rs** + **handlers/request_plugin_install_spec.rs** `[插件MCP]` — 插件安装请求工具。

**handlers/request_user_input.rs** + **handlers/request_user_input_spec.rs** `[工具系统]` — 用户输入请求工具。

**handlers/shell.rs** + **handlers/shell_spec.rs** `[工具系统]` — Shell 命令执行工具。
- `shell/shell_command.rs` — 具体命令执行逻辑

**handlers/test_sync.rs** + **handlers/test_sync_spec.rs** `[辅助]` — 测试同步工具（仅测试用）。

**handlers/tool_search.rs** + **handlers/tool_search_spec.rs** `[工具系统]` — 工具搜索/发现工具。

**handlers/unified_exec.rs** `[工具系统]` — 交互式进程管理工具（exec_command / write_stdin）。
- `unified_exec/exec_command.rs` — exec_command handler
- `unified_exec/write_stdin.rs` — write_stdin handler

**handlers/view_image.rs** + **handlers/view_image_spec.rs** `[工具系统]` — 图片查看工具。

---

### 3.16 tools/runtimes/

底层执行运行时，实际产生进程/应用 patch。

**runtimes/apply_patch.rs** `[工具系统]` — Patch 应用运行时。

**runtimes/mod.rs** `[工具系统]` — 运行时模块入口。

**runtimes/shell.rs** `[进程执行]` — Shell 命令运行时，处理 PTY 分配、输出收集、超时。
- `shell/unix_escalation.rs` — Unix 权限提升逻辑
- `shell/zsh_fork_backend.rs` — zsh fork 后端优化

**runtimes/unified_exec.rs** `[进程执行]` — 交互式进程运行时。

---

### 3.17 tools/tool_family/

工具族分类。

**tools/tool_family/mod.rs** `[工具系统]` — 工具族定义。

**tools/tool_family/shell.rs** `[工具系统]` — Shell 工具族。

---

### 3.18 unified_exec/

交互式进程管理框架，管理长生命周期进程的创建、复用和输出收集。

**unified_exec/async_watcher.rs** `[进程执行]` — 异步输出监控，定时 yield 部分输出给模型。

**unified_exec/errors.rs** `[进程执行]` — 错误类型定义。

**unified_exec/head_tail_buffer.rs** `[进程执行]` — 输出截断缓冲：保留头部 N 行 + 尾部 M 行，中间丢弃。

**unified_exec/mod.rs** `[进程执行]` — 模块入口与核心类型。
- `UnifiedExecContext` struct：执行上下文（session + turn + call_id）
- `ExecCommandRequest` struct / `WriteStdinRequest` struct
- `ProcessStore` struct：进程存储（PID → ProcessEntry 映射）
- `UnifiedExecProcessManager` struct：进程池管理器
- `UnifiedExecProcess` struct：进程句柄
- `SpawnLifecycleHandle` struct

**unified_exec/process.rs** `[进程执行]` — PTY 进程实现，管理单个进程的读写和退出。

**unified_exec/process_manager.rs** `[进程执行]` — 进程池编排，管理多个并发进程的创建和复用。

**unified_exec/process_state.rs** `[进程执行]` — 进程退出/失败状态。

---

### 3.19 utils/

通用工具函数子模块。

**utils/mod.rs** `[辅助]` — 模块入口。

**utils/path_utils.rs** `[辅助]` — 路径处理工具函数。

---

## 4. 模块间调用关系

```
会话管理 (ThreadManager / Session)
├── 调用 → 模型通信 (stream_response)
├── 调用 → 工具系统 (dispatch tool_calls)
├── 调用 → 上下文构建 (build prompt, compact)
├── 调用 → 持久化 (record events, resume)
├── 调用 → 多Agent (spawn/manage agents)
├── 调用 → Hook事件 (lifecycle hooks, event emission)
└── 读取 → 配置系统

工具系统 (ToolRouter / ToolOrchestrator)
├── 调用 → 安全审批 (approval + sandbox selection)
├── 调用 → 进程执行 (spawn processes)
├── 调用 → 插件MCP (mcp_tool_call)
├── 调用 → Hook事件 (pre/post_tool_use)
└── 读取 → 配置系统

安全审批 (Guardian / ExecPolicy)
├── 调用 → 模型通信 (guardian review model session)
└── 读取 → 配置系统

进程执行 (unified_exec / exec / spawn)
├── 调用 → 安全审批 (sandbox parameters)
└── 读取 → 配置系统

插件MCP (McpManager / SkillsManager)
├── 调用 → 安全审批 (mcp tool approval)
└── 读取 → 配置系统
```

---

## 5. 外部 crate 依赖

### 5.1 Workspace 内部 crate

| Crate | 用途 |
|-------|------|
| `codex-api` | 模型 API 通信协议 |
| `codex-protocol` | 协议类型（items、models、events、config_types） |
| `codex-config` | 配置系统基础（Config struct、层叠加载） |
| `codex-features` | Feature flag 定义 |
| `codex-rollout` | 持久化（RolloutRecorder、StateDb） |
| `codex-thread-store` | 线程存储（SQLite/InMemory） |
| `codex-sandboxing` | 沙箱策略与平台适配 |
| `codex-windows-sandbox` | Windows 沙箱实现 |
| `codex-exec-server` | 执行服务器 |
| `codex-execpolicy` | 声明式执行策略引擎 |
| `codex-tools` | 工具定义与 FunctionCallError |
| `codex-apply-patch` | Patch 应用算法 |
| `codex-shell-command` | Shell 命令构建 |
| `codex-model-provider` | 模型提供方抽象 |
| `codex-model-provider-info` | 模型信息（上下文窗口、能力） |
| `codex-models-manager` | 模型管理器 |
| `codex-login` | 认证/登录 |
| `codex-mcp` | MCP 连接管理 |
| `codex-rmcp-client` | RMCP 客户端 |
| `codex-core-plugins` | 插件基础 |
| `codex-core-skills` | 技能管理 |
| `codex-plugin` | 插件接口 |
| `codex-extension-api` | 扩展 API |
| `codex-connectors` | 连接器 |
| `codex-analytics` | 分析事件 |
| `codex-otel` | OpenTelemetry 集成 |
| `codex-memories-read` | 记忆引用解析 |
| `codex-utils-*` | 工具库（path、cache、stream-parser、output-truncation） |

### 5.2 第三方 crate

| Crate | 用途 |
|-------|------|
| `tokio` | 异步运行时（multi-thread、process、signal、fs） |
| `reqwest` | HTTP 客户端（json、stream） |
| `tokio-tungstenite` | WebSocket 客户端 |
| `serde` + `serde_json` + `toml` | 序列化/反序列化 |
| `futures` | 异步原语（Stream、FutureExt） |
| `uuid` | UUID 生成（v4、v5） |
| `chrono` | 日期/时间 |
| `image` | 图片处理（jpeg、png、webp） |
| `rmcp` | MCP SDK |
| `bm25` | 文本搜索排序 |
| `similar` | Diff 算法 |
| `eventsource-stream` | SSE 流解析 |
| `tracing` | 结构化日志/追踪 |
| `anyhow` / `thiserror` | 错误处理 |
| `base64` | Base64 编解码 |
| `tokio-util` | CancellationToken 等 |
