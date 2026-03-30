# `lib.rs` -- network-proxy crate 入口

## 功能概述
network-proxy crate 的库入口文件，声明所有内部模块（certs、config、http_proxy、mitm、network_policy、policy、proxy、reasons、responses、runtime、socks5、state、upstream）并统一导出公共 API。导出内容涵盖配置类型、代理构建器、策略决策接口、运行时状态和常量。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkProxy` | re-export | 代理实例 |
| `NetworkProxyState` | re-export | 代理运行时状态 |
| `NetworkPolicyDecider` | re-export | 策略决策器 trait |
| `NetworkDecision` | re-export | 策略决策结果枚举 |

## 依赖关系
- **被引用方**: codex core 和 CLI 使用网络代理功能

## 实现备注
- 全局禁止 `clippy::print_stdout` 和 `clippy::print_stderr`，所有输出通过 tracing
