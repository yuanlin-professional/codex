# `request_permissions.rs` — 权限请求工具处理器

## 功能概述
该文件实现了 `request_permissions` 工具，允许模型在运行时向用户请求额外的文件系统或网络权限。请求的权限经过规范化和验证后发送给客户端，客户端可以授权全部或部分权限。授权的权限可应用于当前回合后续的 shell 命令，或在客户端批准的情况下持续整个会话。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RequestPermissionsHandler` | struct | 实现 `ToolHandler` trait 的权限请求处理器 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `request_permissions_tool_description` | `pub(crate) fn request_permissions_tool_description() -> String` | 返回工具描述文本 |
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<FunctionToolOutput, FunctionCallError>` | 解析权限请求参数，调用 `normalize_additional_permissions` 规范化权限，验证非空后通过 session 发起权限请求并等待响应 |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_protocol`（`RequestPermissionsArgs`）, `codex_sandboxing`（`normalize_additional_permissions`）
- **内部依赖**: `tools::context`, `tools::registry`, `tools::handlers::parse_arguments_with_base_path`
- **被引用方**: 工具注册表中注册为 `request_permissions` 工具

## 实现备注
- 权限参数中的相对路径会基于当前工作目录进行解析
- 空权限列表会返回错误，要求至少请求一项权限
- 权限请求可能被取消（返回 `None`），此时报告 `RespondToModel` 错误
- 响应被序列化为 JSON 字符串返回给模型
