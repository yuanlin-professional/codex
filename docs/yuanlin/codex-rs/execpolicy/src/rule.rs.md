# `rule.rs` — 规则定义与匹配逻辑

## 功能概述

定义了策略规则的核心类型和匹配逻辑。包括模式匹配 token（单值或替代列表）、前缀匹配模式、前缀规则、网络规则、规则匹配结果等。`Rule` trait 抽象了规则的通用接口，`PrefixRule` 实现了基于命令前缀的匹配逻辑。还包含网络规则的 host 标准化函数和正例/反例验证辅助函数。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PatternToken` | 枚举 | 模式 token：`Single`（单值匹配）或 `Alts`（多值替代匹配） |
| `PrefixPattern` | 结构体 | 前缀匹配模式，包含固定的第一个 token 和后续 token 列表 |
| `PrefixRule` | 结构体 | 前缀匹配规则，包含模式、决策和可选的理由说明 |
| `RuleMatch` | 枚举 | 匹配结果：`PrefixRuleMatch`（前缀规则匹配）或 `HeuristicsRuleMatch`（启发式回退匹配） |
| `NetworkRule` | 结构体 | 网络规则，包含 host、protocol、decision 和 justification |
| `NetworkRuleProtocol` | 枚举 | 网络协议：Http、Https、Socks5Tcp、Socks5Udp |
| `Rule` | trait | 规则通用接口，定义 program()、matches()、as_any() |
| `RuleRef` | 类型别名 | `Arc<dyn Rule>`，规则的共享引用 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `PrefixPattern::matches_prefix` | `(&self, cmd) -> Option<Vec<String>>` | 检查命令是否匹配此前缀模式 |
| `RuleMatch::decision` | `(&self) -> Decision` | 获取匹配结果的决策 |
| `RuleMatch::with_resolved_program` | `(self, resolved_program) -> Self` | 设置解析后的程序路径 |
| `normalize_network_rule_host` | `(raw) -> Result<String>` | 标准化网络规则 host：去端口、去尾点、转小写、拒绝通配符和 URL |
| `validate_match_examples` | `(policy, rules, matches) -> Result<()>` | 验证正例都能匹配至少一条规则 |
| `validate_not_match_examples` | `(policy, rules, not_matches) -> Result<()>` | 验证反例不匹配任何规则 |

## 依赖关系

- **引用的 crate**: `serde`、`shlex`、`codex_utils_absolute_path`
- **被引用方**: `policy.rs`、`parser.rs`

## 实现备注

- `PrefixPattern` 将第一个 token 拆出（`first: Arc<str>`），因为 Policy 按第一个 token 索引规则。
- 网络规则 host 标准化处理了 IPv6 方括号、端口号分离、尾部点号、大小写统一和通配符拒绝。
- `NetworkRuleProtocol::parse` 支持 `"https_connect"` 和 `"http-connect"` 作为 HTTPS 的别名。
