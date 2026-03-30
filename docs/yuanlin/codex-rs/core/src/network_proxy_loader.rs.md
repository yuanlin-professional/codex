# `network_proxy_loader.rs` — 网络代理配置加载与热重载

## 功能概述

此文件负责从 Codex 配置层栈中构建网络代理状态（`NetworkProxyState`），并提供基于文件修改时间（mtime）的配置热重载机制。它从多个配置层（系统层、用户层、项目层）中合并网络策略，应用受信任层的约束验证，并整合执行策略（execpolicy）中的网络域名规则。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `MtimeConfigReloader` | 结构体 | 基于文件 mtime 的配置重载器，实现 `ConfigReloader` trait |
| `LayerMtime` | 结构体（私有） | 记录单个配置层文件路径及其修改时间 |
| `NetworkTablesToml` | 结构体（私有） | 从 TOML 配置中反序列化的网络表格结构 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_network_proxy_state` | `async () -> Result<NetworkProxyState>` | 构建完整的网络代理状态并绑定重载器 |
| `build_network_proxy_state_and_reloader` | `async () -> Result<(ConfigState, MtimeConfigReloader)>` | 返回配置状态与重载器的元组 |
| `config_from_layers` | `(layers, exec_policy) -> Result<NetworkProxyConfig>` | 从配置层栈按优先级合并网络配置 |
| `enforce_trusted_constraints` | `(layers, config) -> Result<NetworkProxyConstraints>` | 从受信任层提取网络约束并验证策略一致性 |
| `apply_network_constraints` | `(network, constraints)` | 将网络配置的各字段叠加到约束对象上 |
| `MtimeConfigReloader::needs_reload` | `async (&self) -> bool` | 检查任一配置文件的 mtime 是否发生变化 |
| `MtimeConfigReloader::maybe_reload` | `async (&self) -> Result<Option<ConfigState>>` | 若检测到变更则重建配置状态 |

## 依赖关系

- **引用的 crate**: `codex_network_proxy`（核心代理类型）、`codex_config`、`codex_app_server_protocol`、`codex_execpolicy`、`anyhow`、`async_trait`、`tokio`
- **被引用方**: `lib.rs` 公开导出此模块，app-server 启动时调用以初始化网络代理

## 实现备注

- 用户控制的层（`User`、`Project`、`SessionFlags`）在约束收集时被跳过，确保受信任层的约束不会被用户配置覆盖
- `MtimeConfigReloader` 使用 `RwLock` 保护层 mtime 列表，支持并发读取和独占写入
- 执行策略中的网络域名规则（允许/拒绝）最后叠加到配置中，优先级最高
- 如果 execpolicy 解析失败，回退到空策略并发出警告，不会中断代理启动
