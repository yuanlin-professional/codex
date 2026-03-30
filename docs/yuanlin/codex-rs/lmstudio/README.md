# lmstudio

## 功能概述

`codex-lmstudio` 是 Codex 项目中与本地 LM Studio 推理服务器进行集成的 crate。它负责:

- **服务器连接验证**: 检测本地 LM Studio 服务是否可达,不可达时提供安装和启动指引。
- **模型管理**: 查询服务器上可用的模型列表,自动下载缺失的模型,后台加载模型到内存。
- **OSS 就绪保障**: 提供 `ensure_oss_ready()` 函数,作为 `--oss` 模式的前置初始化入口,确保本地推理环境准备就绪。

默认 OSS 模型为 `openai/gpt-oss-20b`。

## 架构说明

该 crate 结构简单,由两个模块组成:

1. **lib.rs** -- 公开入口,定义默认模型常量和 `ensure_oss_ready()` 高层初始化函数。该函数按如下流程工作:
   - 从 `Config` 中获取模型名称(或使用默认值)
   - 创建 `LMStudioClient` 并验证服务器连接
   - 查询本地模型列表,若目标模型不存在则下载
   - 在后台 tokio task 中异步加载模型

2. **client.rs** -- `LMStudioClient` 实现,封装与 LM Studio HTTP API 的所有交互:
   - `check_server()`: 通过 `/models` 端点探测服务器可用性
   - `fetch_models()`: 获取已安装模型列表
   - `download_model()`: 通过 `lms get --yes <model>` 命令行工具下载模型
   - `load_model()`: 通过 `/responses` 端点预热模型
   - `find_lms()`: 在 PATH 和平台特定的回退路径中查找 `lms` CLI 工具

## 目录结构

```
lmstudio/
  Cargo.toml
  src/
    lib.rs       # 公开 API,ensure_oss_ready() 入口函数
    client.rs    # LMStudioClient 实现,HTTP 通信与模型管理
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-core` | `Config` 类型、模型提供者配置、`LMSTUDIO_OSS_PROVIDER_ID` 常量 |
| `reqwest` | HTTP 客户端,与 LM Studio REST API 通信 |
| `serde_json` | JSON 响应解析 |
| `tokio` | 异步运行时 |
| `tracing` | 日志记录 |
| `which` | 在 PATH 中查找 `lms` 可执行文件 |

## 核心接口/API

### 公开常量

```rust
/// 默认 OSS 模型名称
pub const DEFAULT_OSS_MODEL: &str = "openai/gpt-oss-20b";
```

### 顶层函数

```rust
/// 确保本地 OSS 推理环境就绪
/// - 验证 LM Studio 服务器可达
/// - 检查目标模型是否存在,不存在则下载
/// - 后台加载模型
pub async fn ensure_oss_ready(config: &Config) -> std::io::Result<()>;
```

### LMStudioClient

```rust
pub struct LMStudioClient { /* ... */ }

impl LMStudioClient {
    /// 从 Config 中的提供者配置创建客户端并验证连接
    pub async fn try_from_provider(config: &Config) -> std::io::Result<Self>;

    /// 获取服务器上的模型列表
    pub async fn fetch_models(&self) -> io::Result<Vec<String>>;

    /// 通过 lms CLI 下载指定模型
    pub async fn download_model(&self, model: &str) -> std::io::Result<()>;

    /// 向服务器发送预热请求以加载模型
    pub async fn load_model(&self, model: &str) -> io::Result<()>;
}
```
