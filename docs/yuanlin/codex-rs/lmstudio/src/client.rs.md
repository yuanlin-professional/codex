# `client.rs` — LM Studio HTTP 客户端

## 功能概述

与本地 LM Studio 服务器通信的 HTTP 客户端。支持服务器健康检查、获取可用模型列表、加载模型（通过发送空请求）和下载模型（通过 `lms` CLI 工具）。从 Config 中的 model_providers 获取 base_url。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LMStudioClient` | struct | HTTP 客户端，包含 reqwest::Client 和 base_url |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `try_from_provider` | `async (config) -> io::Result<Self>` | 从配置中的 provider 构造客户端并检查服务器 |
| `check_server` | `async (&self) -> io::Result<()>` | 通过 GET /models 检查服务器可达性 |
| `fetch_models` | `async (&self) -> io::Result<Vec<String>>` | 获取可用模型 ID 列表 |
| `load_model` | `async (&self, model) -> io::Result<()>` | 发送空请求加载指定模型 |
| `download_model` | `async (&self, model) -> io::Result<()>` | 通过 lms CLI 下载模型 |
| `find_lms` | `() -> io::Result<String>` | 查找 lms 命令（PATH 或回退路径） |

## 依赖关系

- **引用的 crate**: `codex_core`、`reqwest`、`serde_json`、`which`、`wiremock`（测试）
- **被引用方**: `lmstudio/src/lib.rs`
