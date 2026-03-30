# `hierarchical_agents.rs` — 测试模块

## 测试覆盖范围
层级代理（Hierarchical Agents）功能，验证 AGENTS.md 文件内容在用户指令中的注入行为，包括追加到已有项目文档和在无项目文档时独立发送。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `hierarchical_agents_appends_to_project_doc_in_user_instructions` | 启用 ChildAgentsMd 特性后，AGENTS.md 内容追加在项目文档之后的用户指令中 |
| `hierarchical_agents_emits_when_no_project_doc` | 启用 ChildAgentsMd 特性但无 AGENTS.md 文件时，仍然发送层级代理消息片段 |
