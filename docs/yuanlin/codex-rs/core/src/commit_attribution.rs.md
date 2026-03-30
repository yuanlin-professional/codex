# `commit_attribution.rs` — Git 提交归属信息生成

该文件负责生成 Git 提交消息中的 `Co-authored-by` trailer 指令。默认归属值为 `"Codex <noreply@openai.com>"`。`commit_message_trailer_instruction` 函数根据配置生成包含 trailer 格式要求的完整指令文本，用于指导模型在编写或编辑 Git 提交消息时正确附加归属信息。`resolve_attribution_value` 处理配置值的解析：若配置为空字符串则禁用归属，若配置为 `None` 则使用默认值。对应的测试位于 `commit_attribution_tests.rs` 文件中。
