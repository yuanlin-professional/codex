# `discovery.rs` -- Hook 处理器的发现与注册

## 功能概述

本文件实现了从配置层栈（ConfigLayerStack）中发现和加载 Hook 处理器的逻辑。它遍历所有配置层（从低优先级到高优先级），读取每层目录下的 `hooks.json` 文件，解析并验证其中的 Hook 处理器，最终将它们收集为 `ConfiguredHandler` 列表。不合法的配置会被记录为警告而非报错，保证系统的健壮性。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `DiscoveryResult` | struct | 发现结果，包含合法的 handlers 列表和警告信息列表 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `discover_handlers` | `fn(config_layer_stack) -> DiscoveryResult` | 遍历所有配置层发现 Hook 处理器 |
| `append_matcher_groups` | `fn(handlers, warnings, display_order, source_path, event_name, groups)` | 将多个 matcher group 展平后追加到 handlers |
| `append_group_handlers` | `fn(handlers, warnings, display_order, source_path, event_name, matcher, group_handlers)` | 处理单个 matcher group：验证 matcher、过滤不支持的类型、注册 command 类型 handler |

## 依赖关系

- **引用的 crate**: `codex_config`（ConfigLayerStack、ConfigLayerStackOrdering）、`codex_protocol`（HookEventName）
- **引用的内部模块**: `engine::ConfiguredHandler`、`engine::config`（HooksFile、HookEvents 等）、`events::common`（matcher 验证）
- **被引用方**: `engine::mod.rs` 中的 `ClaudeHooksEngine::new`

## 实现备注

- display_order 跨所有配置层递增，用于维持处理器的执行顺序。
- async Hook 当前不支持，发现时会生成警告并跳过。
- 空命令字符串也会被跳过并生成警告。
- timeout 默认 600 秒，最小 1 秒。
- 无效的 matcher 正则会导致整个 matcher group 被跳过（但 `UserPromptSubmit` 和 `Stop` 事件会忽略 matcher，所以不会触发此逻辑）。
- 内嵌测试验证了各事件类型的 matcher 在 discovery 阶段的行为。
