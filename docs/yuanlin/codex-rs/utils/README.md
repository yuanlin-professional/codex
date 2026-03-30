# utils/

## 功能概述

`utils/` 是 Codex 项目 Rust 端 (`codex-rs`) 的通用工具库容器目录，包含 22 个独立的子 crate。这些 crate 提供了从路径处理、缓存、图像编码到模板渲染等各类基础功能，被 Codex 的核心模块（`codex-core`）、TUI 前端、CLI 工具以及 MCP 服务端等上层组件广泛复用。

每个子 crate 以 `codex-utils-*` 为 crate 名称发布，遵循 workspace 统一版本管理。

## 目录结构

```
utils/
├── absolute-path/       # 绝对路径类型与序列化支持
├── approval-presets/    # 审批策略预设（只读/默认/完全访问）
├── cache/               # 基于 Tokio Mutex 的 LRU 缓存
├── cargo-bin/           # 测试中定位构建产物二进制文件
├── cli/                 # CLI 参数解析辅助类型（clap 集成）
├── elapsed/             # 时间格式化（毫秒/秒/分钟）
├── fuzzy-match/         # 大小写不敏感的模糊子序列匹配
├── home-dir/            # Codex 配置目录定位（~/.codex）
├── image/               # 图像加载、缩放与 Base64 编码
├── json-to-toml/        # JSON 值到 TOML 值的转换
├── oss/                 # OSS 提供商（LM Studio / Ollama）工具
├── output-truncation/   # 输出截断策略（按字节/按 token）
├── path-utils/          # 路径规范化、符号链接解析与原子写入
├── plugins/             # 插件命名空间解析与提及语法
├── pty/                 # PTY / 管道进程创建与管理
├── readiness/           # 基于 Token 的异步就绪标志
├── rustls-provider/     # rustls 加密提供者初始化
├── sandbox-summary/     # 沙箱策略与配置摘要生成
├── sleep-inhibitor/     # 跨平台系统休眠抑制
├── stream-parser/       # 流式文本/引用/计划块解析器
├── string/              # 字符串截断、UUID 查找与度量标签清洗
└── template/            # 严格模板引擎（{{ name }} 占位符）
```

## 子 crate 一览

| # | 目录名 | crate 名 | 简要说明 |
|---|--------|----------|----------|
| 1 | `absolute-path` | `codex-utils-absolute-path` | 保证绝对且规范化的路径类型 |
| 2 | `approval-presets` | `codex-utils-approval-presets` | 内置审批/沙箱策略预设列表 |
| 3 | `cache` | `codex-utils-cache` | Tokio 互斥锁保护的 LRU 缓存 |
| 4 | `cargo-bin` | `codex-utils-cargo-bin` | Cargo/Bazel 环境下定位测试二进制 |
| 5 | `cli` | `codex-utils-cli` | clap 集成的 CLI 参数辅助类型 |
| 6 | `elapsed` | `codex-utils-elapsed` | 人类可读的时间间隔格式化 |
| 7 | `fuzzy-match` | `codex-utils-fuzzy-match` | Unicode 安全的模糊子序列匹配 |
| 8 | `home-dir` | `codex-utils-home-dir` | 定位 Codex 主目录（支持 CODEX_HOME） |
| 9 | `image` | `codex-utils-image` | 图像处理、缩放与 data URL 编码 |
| 10 | `json-to-toml` | `codex-utils-json-to-toml` | serde_json Value 到 toml Value 转换 |
| 11 | `oss` | `codex-utils-oss` | OSS 模型提供商就绪检查与默认模型 |
| 12 | `output-truncation` | `codex-utils-output-truncation` | 工具/exec 输出的智能截断 |
| 13 | `path-utils` | `codex-utils-path` | 路径规范化、WSL 处理与原子文件写入 |
| 14 | `plugins` | `codex-utils-plugins` | 插件清单解析与 mention 语法常量 |
| 15 | `pty` | `codex-utils-pty` | 伪终端/管道进程生命周期管理 |
| 16 | `readiness` | `codex-utils-readiness` | Token 授权的异步就绪信号 |
| 17 | `rustls-provider` | `codex-utils-rustls-provider` | 进程级 rustls 加密后端安装 |
| 18 | `sandbox-summary` | `codex-utils-sandbox-summary` | 沙箱策略和配置的文本摘要 |
| 19 | `sleep-inhibitor` | `codex-utils-sleep-inhibitor` | macOS/Linux/Windows 防止系统休眠 |
| 20 | `stream-parser` | `codex-utils-stream-parser` | 流式 AI 文本/引用/计划解析 |
| 21 | `string` | `codex-utils-string` | 字符串截断、UUID 提取与标签清洗 |
| 22 | `template` | `codex-utils-template` | 严格 mustache 风格模板渲染 |

## 依赖关系

各子 crate 之间存在少量内部依赖：

- `codex-utils-image` 依赖 `codex-utils-cache`（图像缓存）
- `codex-utils-output-truncation` 依赖 `codex-utils-string`（截断实现）
- `codex-utils-path` 依赖 `codex-utils-absolute-path`（路径类型）
- `codex-utils-sandbox-summary` 依赖 `codex-core` 和 `codex-protocol`
- `codex-utils-oss` 依赖 `codex-core`、`codex-lmstudio`、`codex-ollama`

大部分子 crate 是叶子节点，没有对其他 utils crate 的依赖，可以独立使用。
