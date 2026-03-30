# codex-rs/.config -- 测试运行器配置

## 功能概述

`codex-rs/.config/` 目录包含 Codex Rust 工作空间的测试运行器 nextest 的配置文件，用于控制测试超时、并发和分组策略。

## 目录结构

```
codex-rs/.config/
└── nextest.toml    # cargo-nextest 测试运行器配置
```

## 配置说明

### nextest.toml

[cargo-nextest](https://nexte.st/) 是 Codex 项目使用的 Rust 测试运行器，提供比 `cargo test` 更好的并行执行和超时控制能力。

#### 默认配置
- **慢测试超时**：15 秒周期，超过 2 个周期（30 秒）后强制终止
- **原则**：不应增加默认超时，而应修复超时的测试

#### 特殊超时规则
| 测试匹配条件 | 超时配置 |
|--------------|----------|
| `rmcp_client` 或 `humanlike_typing_1000_chars_appears_live_no_placeholder` | 1 分钟周期，4 个周期后终止 |
| `approval_matrix_covers_all_modes` | 30 秒周期，2 个周期后终止 |

#### 测试分组（限制并发）
| 分组名称 | 最大线程数 | 匹配条件 |
|----------|-----------|----------|
| `app_server_protocol_codegen` | 1 | `codex-app-server-protocol` 包中的 schema 生成测试 |
| `app_server_integration` | 1 | `codex-app-server` 包中的集成测试（每个用例启动独立子进程） |

设置为单线程是因为：协议代码生成测试需要顺序执行以避免竞争，集成测试每个用例都会启动独立的 app-server 子进程。

## 依赖关系

- **工具**：cargo-nextest
- **被调用方**：`cargo nextest run`、CI 工作流中的测试步骤
- **影响范围**：整个 codex-rs 工作空间的测试执行行为
