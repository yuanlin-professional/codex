# `apply_patch.rs` -- 文件补丁应用工具处理器

## 功能概述
该文件实现了 `apply_patch` 工具的处理器，用于接收模型生成的补丁文本并将其应用到文件系统。它支持两种调用方式：通过函数工具（JSON 参数）和自定义工具（freeform 语法）。处理器会解析补丁、计算受影响文件的权限、确定是否需要审批，然后执行补丁操作。此外还提供了拦截函数 `intercept_apply_patch`，用于在 `exec_command` 中检测并接管 apply_patch 调用。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ApplyPatchHandler` | struct | 实现 `ToolHandler` trait，作为 `apply_patch` 工具的主处理器 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ApplyPatchHandler::handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<ApplyPatchToolOutput, FunctionCallError>` | 解析补丁输入、验证补丁正确性、计算权限并执行补丁。支持直接应用和委托给 exec 两种路径 |
| `intercept_apply_patch` | `async fn intercept_apply_patch(command, cwd, ...) -> Result<Option<FunctionToolOutput>, FunctionCallError>` | 从 exec_command 的命令向量中检测 apply_patch 调用，若匹配则拦截处理，否则返回 None |
| `file_paths_for_action` | `fn file_paths_for_action(action: &ApplyPatchAction) -> Vec<AbsolutePathBuf>` | 从补丁动作中提取所有受影响的文件路径（包括移动目标路径） |
| `write_permissions_for_paths` | `fn write_permissions_for_paths(file_paths, sandbox_policy, cwd) -> Option<PermissionProfile>` | 计算需要额外写权限的路径，过滤掉沙盒策略已允许写入的目录 |
| `effective_patch_permissions` | `async fn effective_patch_permissions(session, turn, action) -> (Vec, EffectiveAdditionalPermissions, FileSystemSandboxPolicy)` | 综合计算补丁操作的有效权限，合并会话级和回合级授权 |
| `create_apply_patch_freeform_tool` | `fn create_apply_patch_freeform_tool() -> ToolSpec` | 创建基于 Lark 语法的 freeform 工具定义，适用于 GPT-5 模型 |
| `create_apply_patch_json_tool` | `fn create_apply_patch_json_tool() -> ToolSpec` | 创建基于 JSON schema 的工具定义，适用于 gpt-oss 模型，描述中包含完整的补丁语法说明 |

## 依赖关系
- **引用的 crate**: `codex_apply_patch`（补丁解析和验证）、`codex_protocol`（权限模型、沙盒策略）、`codex_sandboxing`（权限合并和策略计算）、`codex_utils_absolute_path`（绝对路径处理）、`async_trait`
- **被引用方**: 工具注册表注册为 `apply_patch` 工具处理器；`exec_command` 处理器通过 `intercept_apply_patch` 调用；工具规格创建函数被工具定义模块引用

## 实现备注
- 补丁验证和执行分为两步：先通过 `maybe_parse_apply_patch_verified` 进行格式验证和解析，再通过 `apply_patch::apply_patch` 判断是直接应用还是委托给 exec 运行时。
- 权限计算采用层叠式合并：会话授权 + 回合授权 -> 有效沙盒策略 -> 需要额外写权限的路径。
- `intercept_apply_patch` 函数在检测到 exec_command 中嵌套 apply_patch 调用时会记录模型警告，引导模型使用专用工具。
- freeform 工具使用 Lark 语法文件（`tool_apply_patch.lark`）定义输入格式，通过 `include_str!` 编译时嵌入。
- `is_mutating` 始终返回 `true`，因为补丁操作会修改文件系统。
