# sandbox-summary

## 功能概述

`codex-utils-sandbox-summary` 提供了将 Codex 的沙箱策略和运行配置转换为人类可读文本摘要的功能。用于 TUI 显示、日志输出等需要呈现当前配置状态的场景。

## 目录结构

```
sandbox-summary/
├── Cargo.toml
└── src/
    ├── lib.rs              # 模块导出
    ├── sandbox_summary.rs  # 沙箱策略摘要
    └── config_summary.rs   # 完整配置摘要
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-core` | Config 类型和 WireApi 枚举 |
| `codex-protocol` | SandboxPolicy、NetworkAccess 等协议类型 |

## 核心接口/API

### `summarize_sandbox_policy(sandbox_policy: &SandboxPolicy) -> String`

将沙箱策略转换为简洁的文本描述。

输出示例：
- `"danger-full-access"`
- `"read-only"`
- `"read-only (network access enabled)"`
- `"external-sandbox"`
- `"workspace-write [workdir, /tmp, $TMPDIR, /repo] (network access enabled)"`

### `create_config_summary_entries(config: &Config, model: &str) -> Vec<(&'static str, String)>`

构建一个键值对列表，总结当前的有效配置。

返回的条目包括：
| 键 | 说明 |
|----|------|
| `workdir` | 工作目录 |
| `model` | 模型名称 |
| `provider` | 模型提供商 ID |
| `approval` | 审批策略 |
| `sandbox` | 沙箱策略摘要 |
| `reasoning effort`（可选） | 推理努力程度（仅 Responses API） |
| `reasoning summaries`（可选） | 推理摘要设置（仅 Responses API） |
