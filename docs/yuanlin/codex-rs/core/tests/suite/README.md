# tests/suite/ -- 测试套件

## 功能概述

`suite/` 包含 Codex core crate 的全部集成测试用例，覆盖所有核心功能模块。每个测试文件聚焦一个特定功能领域，通过模拟 Responses API 服务器验证端到端行为。测试使用 `insta` 快照测试确保输出的稳定性。

## 目录结构

```
suite/
├── mod.rs                          # 测试套件模块入口
├── compact.rs / compact_remote.rs  # 上下文压缩测试
├── exec.rs / shell_command.rs      # 命令执行测试
├── apply_patch_cli.rs              # 补丁应用测试
├── approvals.rs                    # 审批流程测试
├── hooks.rs                        # 钩子系统测试
├── unified_exec.rs                 # 统一执行测试
├── hierarchical_agents.rs          # 层级代理测试
├── agent_jobs.rs                   # 代理任务测试
├── code_mode.rs                    # 代码模式测试
├── js_repl.rs                      # JavaScript REPL 测试
├── plugins.rs                      # 插件系统测试
├── skills.rs                       # 技能系统测试
├── memories.rs                     # 记忆系统测试
├── review.rs                       # 代码审查测试
├── resume.rs                       # 会话恢复测试
├── web_search.rs                   # 网络搜索测试
├── tools.rs                        # 工具系统测试
├── model_info_overrides.rs         # 模型信息覆盖测试
├── personality.rs                  # 人格系统测试
├── permissions_messages.rs         # 权限消息测试
├── request_permissions.rs          # 权限请求测试
├── truncation.rs                   # 截断策略测试
├── otel.rs                         # OpenTelemetry 测试
├── snapshots/                      # insta 快照文件目录
└── ... (70+ 测试文件)
```

## 依赖关系

- **上游依赖**：`insta`（快照测试）、`common/`（测试辅助库）
- **内部依赖**：`tests/common/`（测试基础设施）、`tests/fixtures/`（测试数据）

## 核心接口/API

| 文件分类 | 覆盖功能 |
|---|---|
| 会话生命周期 | `client.rs`、`resume.rs`、`fork_thread.rs`、`compact.rs` |
| 工具执行 | `exec.rs`、`shell_command.rs`、`unified_exec.rs`、`apply_patch_cli.rs` |
| 多代理 | `hierarchical_agents.rs`、`agent_jobs.rs`、`spawn_agent_description.rs` |
| 安全与审批 | `approvals.rs`、`request_permissions.rs`、`seatbelt.rs`、`safety_check_downgrade.rs` |
| 模型与配置 | `model_info_overrides.rs`、`model_switching.rs`、`personality.rs` |
| 集成功能 | `plugins.rs`、`skills.rs`、`memories.rs`、`hooks.rs`、`web_search.rs` |
