# `device_code_auth.rs` — 设备码登录流程

## 功能概述
实现 OAuth 设备码授权流程：请求用户码、显示终端提示（含验证 URL 和一次性码）、轮询 token 端点直到用户完成认证、然后使用授权码换取 token 并持久化。支持 15 分钟超时。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `DeviceCode` | 结构体 | 设备码信息：verification_url, user_code, device_auth_id, interval |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `request_device_code` | `async fn(opts) -> io::Result<DeviceCode>` | 请求设备码 |
| `complete_device_code_login` | `async fn(opts, device_code) -> io::Result<()>` | 完成设备码登录 |
| `run_device_code_login` | `async fn(opts) -> io::Result<()>` | 完整设备码登录流程 |

## 依赖关系
- **引用的 crate**: `reqwest`, `codex_client`, `serde`
- **被引用方**: CLI 登录命令
