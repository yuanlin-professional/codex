# `headless_chatgpt_login.rs` — 无头 ChatGPT 设备码登录

## 功能概述

本模块实现通过设备码（Device Code）进行 ChatGPT 认证的无头登录流程，适用于远程或无浏览器的机器。它请求设备码、显示验证 URL 和用户码、等待用户在浏览器中完成认证，然后通过本地 auth token 完成登录。如果设备码端点不可用，会自动回退到浏览器登录。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_headless_chatgpt_login` | `(widget)` | 发起设备码登录，在后台任务中运行 |
| `render_device_code_login` | `(widget, area, buf, state)` | 渲染设备码登录界面（含验证 URL 和用户码） |
| `fallback_to_browser_login` | `async (...)` | 设备码不可用时回退到浏览器登录 |
| `handle_chatgpt_auth_tokens_login_result_for_active_attempt` | `async (...)` | 处理本地 auth token 登录结果 |

## 依赖关系

- **引用的 crate**: `codex_login`, `codex_core::auth`, `codex_app_server_protocol`
- **被引用方**: `AuthModeWidget::start_device_code_login`

## 实现备注

- 使用 `Arc<Notify>` 实现取消机制，用户按 Esc 时通知异步任务
- 通过 `device_code_attempt_matches` 确保只更新当前活跃的登录尝试
- 设备码 15 分钟过期
- 渲染中包含 OSC 8 超链接标记
