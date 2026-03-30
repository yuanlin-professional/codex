# `oss_selection.rs` — 开源 LLM 提供商选择界面

## 功能概述

实现一个终端 UI 界面，让用户在本地开源 AI 服务器（LM Studio、Ollama）之间选择。
启动时自动检测各提供商的运行状态（通过端口检查），如果只有一个在运行则自动选择。
两者都在运行或都未运行时显示交互式选择界面。用户选择后将偏好持久化到配置文件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `OssSelectionWidget` | struct | 提供商选择交互组件 |
| `ProviderOption` | struct | 提供商名称和状态 |
| `ProviderStatus` | enum | 运行中/未运行/未知 |
| `SelectOption` | struct | 选择菜单项（标签、描述、按键、ID） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `select_oss_provider` | `async (codex_home) -> Result<String>` | 主入口：检测+选择+持久化 |
| `handle_key_event` | `(&mut self, key) -> Option<String>` | 处理按键事件 |
| `check_lmstudio_status/check_ollama_status` | `async -> ProviderStatus` | 检测服务状态 |

## 依赖关系

- **引用的 crate**: `codex_core`, `crossterm`, `ratatui`, `reqwest`

## 实现备注

- 使用 `reqwest` HTTP 客户端（2 秒超时）探测本地端口判断服务是否运行。
- 界面使用备用屏幕（alternate screen），退出后不留痕迹。
- 按键支持 Left/Right 切换、Enter 确认、Esc 默认选 LM Studio、字母快速选择。
