# `debug_config.rs` — 调试配置输出生成

## 功能概述

本文件实现了 `/debug-config` 命令的输出生成，用于显示当前配置层栈（按优先级排列）、各层的启用/禁用状态、配置要求（allowed_approval_policies、allowed_sandbox_modes、mcp_servers、enforce_residency、network 约束等）以及会话运行时信息（网络代理地址）。输出以带样式的 ratatui `Line` 列表形式返回。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new_debug_config_output` | `fn(config, session_network_proxy) -> PlainHistoryCell` | 生成完整的调试配置输出 |
| `render_debug_config_lines` | `fn(stack) -> Vec<Line<'static>>` | 渲染配置层栈和要求为样式化行 |

## 依赖关系

- **引用的 crate**: `codex_core::config`、`codex_core::config_loader`（ConfigLayerStack、各种要求类型）、`ratatui`
- **被引用方**: `app.rs` 中处理 `/debug-config` 斜杠命令

## 实现备注

- 配置层按最低优先级在前排列，每层显示来源（system/user/project/MDM/session-flags）和启用状态。
- MDM 层会显示原始 TOML 值；session-flags 层会展开为键值对。
- 网络约束的展示包括 HTTP/SOCKS 端口、域名规则、Unix 套接字权限等。
- 包含大量单元测试验证输出格式和内容正确性。
