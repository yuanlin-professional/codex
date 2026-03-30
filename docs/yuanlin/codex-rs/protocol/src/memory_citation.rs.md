# `memory_citation.rs` — 记忆引用类型

`MemoryCitation` 和 `MemoryCitationEntry` 结构体定义，用于在 Agent 消息中标注引用的记忆条目。`MemoryCitation` 包含引用条目列表和关联的 rollout ID 列表；每个 `MemoryCitationEntry` 记录了引用源文件路径、行范围（起始行/结束行）和备注说明。
