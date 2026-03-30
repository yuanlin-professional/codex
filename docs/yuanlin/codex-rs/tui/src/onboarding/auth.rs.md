# `auth.rs` — 认证流程 UI 组件

## 功能概述

本模块实现 TUI 引导（onboarding）流程中的认证步骤，支持三种登录方式：ChatGPT 浏览器登录、设备码（Device Code）登录和 API Key 手动输入。它管理认证状态机的转换、UI 渲染、键盘事件处理和异步登录请求。还提供了 `mark_url_hyperlink` 工具函数，用于在终端缓冲区中标记 OSC 8 超链接。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SignInState` | enum | 登录状态：PickMode、ChatGptContinueInBrowser、ChatGptDeviceCode、ChatGptSuccessMessage、ChatGptSuccess、ApiKeyEntry、ApiKeyConfigured |
| `SignInOption` | enum | 登录选项：ChatGpt、DeviceCode、ApiKey |
| `AuthModeWidget` | struct | 认证模式 UI 组件，包含状态、错误、配置等 |
| `ApiKeyInputState` | struct | API Key 输入状态（值和是否从环境变量预填充） |
| `ContinueInBrowserState` | struct | 浏览器登录状态（login_id 和 auth_url） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_chatgpt_login` | `(&mut self)` | 发起 ChatGPT 浏览器登录流程 |
| `start_device_code_login` | `(&mut self)` | 发起设备码登录流程 |
| `save_api_key` | `(&mut self, api_key)` | 保存 API Key 并验证 |
| `cancel_active_attempt` | `(&self)` | 取消当前进行中的登录尝试 |
| `mark_url_hyperlink` | `(buf, area, url)` | 将 cyan+underlined 样式的单元格标记为 OSC 8 超链接 |

## 依赖关系

- **引用的 crate**: `codex_app_server_client`, `codex_app_server_protocol`, `codex_core::auth`, `codex_login`
- **被引用方**: `OnboardingScreen` 认证步骤

## 实现备注

- `forced_login_method` 可强制限制只允许 ChatGPT 或 API Key 登录
- API Key 支持从 `OPENAI_API_KEY` 环境变量预填充
- URL 中的 ESC 和 BEL 字符会被过滤以防止终端转义注入
- 包含 OSC 8 超链接渲染测试和 URL 安全性测试
