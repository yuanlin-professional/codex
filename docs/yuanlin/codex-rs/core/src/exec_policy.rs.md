# `exec_policy.rs` — 命令执行策略管理与审批决策引擎

## 功能概述

该文件实现了 Codex 的命令执行策略（exec policy）系统，负责判断用户或模型提交的 shell 命令是否可以直接执行、需要用户审批还是应被禁止。它从配置层级栈中加载 `.rules` 策略文件，通过 `ExecPolicyManager` 管理策略的热加载和动态更新（如追加允许前缀规则和网络规则），并根据沙箱策略、审批策略和危险命令检测综合得出最终决策（Allow / Prompt / Forbidden）。同时，该模块还负责为需要审批的命令生成人类可读的原因说明，以及自动推导可能的策略修正（amendment）建议。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecPolicyManager` | struct | 策略管理器，持有 `ArcSwap<Policy>` 实现并发安全的策略热更新，并通过 `Mutex` 保护写操作的串行化 |
| `ExecApprovalRequest` | struct | 命令审批请求，包含命令参数、审批策略、沙箱策略、文件系统沙箱策略、沙箱权限及前缀规则 |
| `ExecPolicyError` | enum | 策略加载阶段的错误类型，包括读目录失败、读文件失败、解析策略失败 |
| `ExecPolicyUpdateError` | enum | 策略运行时更新的错误类型，包括追加规则失败、阻塞任务连接失败、内存规则添加失败 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ExecPolicyManager::load` | `async fn load(config_stack: &ConfigLayerStack) -> Result<Self, ExecPolicyError>` | 从配置层级栈异步加载并构建策略管理器 |
| `create_exec_approval_requirement_for_command` | `async fn(&self, req: ExecApprovalRequest<'_>) -> ExecApprovalRequirement` | 核心方法：根据策略评估命令，返回 Skip/NeedsApproval/Forbidden |
| `append_amendment_and_update` | `async fn(&self, codex_home: &Path, amendment: &ExecPolicyAmendment) -> Result<()>` | 将允许前缀规则追加到磁盘策略文件并更新内存策略 |
| `append_network_rule_and_update` | `async fn(&self, ...) -> Result<()>` | 追加网络规则到磁盘并更新内存策略 |
| `render_decision_for_unmatched_command` | `fn(...) -> Decision` | 未匹配任何规则时的默认决策逻辑，综合考虑沙箱和审批策略 |
| `load_exec_policy` | `async fn(config_stack: &ConfigLayerStack) -> Result<Policy, ExecPolicyError>` | 从所有配置层的 `rules/` 目录加载 `.rules` 文件并合并为统一策略 |
| `prompt_is_rejected_by_policy` | `fn(approval_policy, prompt_is_rule) -> Option<&'static str>` | 判断审批策略是否拒绝向用户展示提示 |
| `commands_for_exec_policy` | `fn(command: &[String]) -> (Vec<Vec<String>>, bool)` | 解析 shell 命令为可被策略引擎评估的命令列表 |

## 依赖关系

- **引用的 crate**: `codex_execpolicy`（策略引擎核心）、`codex_protocol`（协议类型如 `SandboxPolicy`、`AskForApproval`）、`arc_swap`（无锁原子指针交换）、`thiserror`、`tokio`、`tracing`、`shlex`
- **被引用方**: `crate::tools::sandboxing`（使用 `ExecApprovalRequirement`）、`crate::codex`（会话层加载并使用策略管理器）、`crate::config`（配置系统提供层级栈）

## 实现备注

- 使用 `ArcSwap<Policy>` 实现策略的无锁读取和原子替换，读操作完全无阻塞。
- 策略文件的磁盘写入通过 `spawn_blocking` 在阻塞线程池中执行，避免阻塞 Tokio 运行时。
- `BANNED_PREFIX_SUGGESTIONS` 列表防止模型建议过于宽泛的前缀规则（如 `python3`、`git`、`bash` 等）。
- `commands_for_exec_policy` 会尝试将 `bash -lc "cmd1 && cmd2"` 形式的复合命令拆解为独立命令分别评估，同时标记是否使用了复杂解析以决定是否允许自动修正建议。
- 策略层级按优先级从低到高加载，高优先级层可以覆盖低优先级层的规则。
