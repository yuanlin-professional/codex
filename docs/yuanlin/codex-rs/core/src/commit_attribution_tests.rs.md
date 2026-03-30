# `commit_attribution_tests.rs` — 提交归属（commit attribution）逻辑测试

测试 git 提交消息中归属信息（trailer）的构建与解析。验证空白归属值禁用 trailer、默认归属使用 Codex 签名、`resolve_attribution_value` 正确处理默认/自定义/空白输入，以及 `commit_message_trailer_instruction` 生成的指令包含正确的 Co-authored-by 行且不包含已弃用的 Generated-with 标记。
