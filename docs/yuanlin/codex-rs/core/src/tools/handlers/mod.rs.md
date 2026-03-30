# `mod.rs` — 工具处理器模块入口

## 功能概述
该文件是 `tools::handlers` 模块的入口文件，负责组织和导出所有工具处理器子模块。除了模块声明和 re-export 外，还定义了若干通用的参数解析工具函数、权限验证与归一化逻辑、以及子代理生成时的配置管理函数。这些函数被多个具体的工具处理器共享使用。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `EffectiveAdditionalPermissions` | struct | 封装经过权限合并和预审后生效的额外权限信息，包含 `sandbox_permissions`、`additional_permissions` 和 `permissions_preapproved` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_arguments` | `fn<T: Deserialize>(arguments: &str) -> Result<T, FunctionCallError>` | 从 JSON 字符串反序列化为目标类型的通用解析函数 |
| `parse_arguments_with_base_path` | `fn<T: Deserialize>(arguments: &str, base_path: &Path) -> Result<T, FunctionCallError>` | 在设置绝对路径上下文后解析参数，用于路径感知的反序列化 |
| `resolve_workdir_base_path` | `fn(arguments: &str, default_cwd: &Path) -> Result<PathBuf, FunctionCallError>` | 从参数中提取 `workdir` 字段并解析为绝对路径，缺失时使用默认工作目录 |
| `normalize_and_validate_additional_permissions` | `fn(...) -> Result<Option<PermissionProfile>, String>` | 验证和归一化 `with_additional_permissions` 的权限请求，确保特性开关和审批策略合规 |
| `implicit_granted_permissions` | `fn(...) -> Option<PermissionProfile>` | 判断是否可以使用隐式的粘性权限授予路径 |
| `apply_granted_turn_permissions` | `async fn(session, sandbox_permissions, additional_permissions) -> EffectiveAdditionalPermissions` | 合并会话级和轮次级已授予权限，计算有效权限和预审状态 |

## 依赖关系
- **引用的 crate**: `serde`, `serde_json`, `codex_sandboxing::policy_transforms::*`, `codex_utils_absolute_path`, `codex_protocol`
- **引用的内部模块**: `crate::codex::Session`, `crate::function_tool::FunctionCallError`, `crate::sandboxing::SandboxPermissions`
- **子模块**: `agent_jobs`, `apply_patch`, `dynamic`, `js_repl`, `list_dir`, `mcp`, `mcp_resource`, `multi_agents`, `multi_agents_common`, `multi_agents_v2`, `plan`, `request_permissions`, `request_user_input`, `shell`, `test_sync`, `tool_search`, `tool_suggest`, `unified_exec`, `view_image`
- **导出的处理器**: `ApplyPatchHandler`, `DynamicToolHandler`, `JsReplHandler`, `ListDirHandler`, `McpHandler`, `McpResourceHandler`, `PlanHandler`, `RequestPermissionsHandler`, `RequestUserInputHandler`, `ShellHandler`, `ShellCommandHandler`, `TestSyncHandler`, `ToolSearchHandler`, `ToolSuggestHandler`, `UnifiedExecHandler`, `ViewImageHandler` 等

## 实现备注
- 权限验证区分"预审（preapproved）"和"新请求"两种场景，预审权限可以绕过 `exec_permission_approvals` 特性开关
- `apply_granted_turn_permissions` 使用 `merge_permission_profiles` 和 `intersect_permission_profiles` 实现权限合并和子集检测
- `RequireEscalated` 沙箱模式下直接跳过权限合并逻辑
- 文件末尾包含内联测试模块，覆盖权限验证的各种边界情况
