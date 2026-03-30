# `plugins.rs` — 插件管理 UI 逻辑

## 功能概述

本模块实现了 TUI 中 `/plugins` 命令的全部 UI 逻辑，包括插件列表的加载、缓存、展示，单个插件详情的查看，以及插件的安装、卸载流程。它还管理插件安装后的 App 认证流程（逐个引导用户在 ChatGPT 中安装所需的 Apps）。所有 UI 通过 `SelectionViewParams` 驱动 bottom_pane 的弹出式选择视图。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PluginsCacheState` | enum | 插件缓存状态：Uninitialized、Loading、Ready、Failed |
| `DelayedLoadingHeader` | struct | 延迟加载动画头部，在 1 秒后显示 shimmer 动画 |
| `PluginDisclosureLine` | struct | 插件安装前的数据共享声明行 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `add_plugins_output` | `(&mut self)` | `/plugins` 命令入口，展示插件列表或加载界面 |
| `on_plugins_loaded` | `(&mut self, cwd, result)` | 处理插件列表加载完成回调 |
| `on_plugin_install_loaded` | `(&mut self, cwd, ..., result) -> bool` | 处理插件安装完成，可能启动 App 认证流程 |
| `advance_plugin_install_auth_flow` | `(&mut self)` | 推进插件安装后的 App 认证流程 |
| `plugins_popup_params` | `(&self, response) -> SelectionViewParams` | 构建插件列表弹窗参数 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol` (Plugin* 类型), `codex_features::Feature`
- **被引用方**: `ChatWidget` 和 `App` 事件处理逻辑

## 实现备注

- 插件按"已安装优先、名称字母序"排序
- 支持搜索过滤功能
- 安装后如有 Apps 需要认证，会逐个引导用户通过浏览器安装
- 使用 cwd 作为缓存键，切换目录时重新加载
