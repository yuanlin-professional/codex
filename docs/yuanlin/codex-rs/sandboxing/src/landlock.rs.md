# `landlock.rs` — Linux 沙箱命令行参数构建

## 功能概述

负责构建 `codex-linux-sandbox` 辅助程序的命令行参数。该辅助程序执行实际的 Linux 沙箱操作（默认使用 bubblewrap + seccomp）。提供两个核心函数：`create_linux_sandbox_command_args_for_policies` 接受完整的沙箱策略 JSON 并构建参数列表，`create_linux_sandbox_command_args` 是简化版本。支持旧版 Landlock 标志和代理网络标志等可选特性。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CODEX_LINUX_SANDBOX_ARG0` | 常量 | Linux 沙箱辅助程序的 argv[0] 名称: `"codex-linux-sandbox"` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `allow_network_for_proxy` | `(enforce_managed_network) -> bool` | 判断是否需要为代理启用网络访问 |
| `create_linux_sandbox_command_args_for_policies` | `(command, command_cwd, sandbox_policy, file_system_policy, network_policy, policy_cwd, use_legacy_landlock, allow_network_for_proxy) -> Vec<String>` | 构建完整的沙箱辅助程序命令行参数（含策略 JSON） |
| `create_linux_sandbox_command_args` | `(command, command_cwd, policy_cwd, use_legacy_landlock, allow_network_for_proxy) -> Vec<String>` | 构建简化版本的沙箱命令行参数（仅 cwd 和标志） |

## 依赖关系

- **引用的 crate**: `codex_protocol`
- **被引用方**: `manager.rs`

## 实现备注

- 策略参数以 JSON 字符串形式传递给辅助程序。
- 使用 `--` 分隔沙箱选项和被执行的原始命令，防止以 `-` 开头的命令参数被误解析为沙箱选项。
- `allow_network_for_proxy` 仅在启用托管网络要求时才请求代理网络。
