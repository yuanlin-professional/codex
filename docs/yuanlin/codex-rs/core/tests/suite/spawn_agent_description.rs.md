# `spawn_agent_description.rs` -- 测试模块

## 测试覆盖范围
测试 spawn_agent 工具描述中对可见模型、推理级别和授权规则的展示逻辑。验证远程模型列表中可见模型被列出、隐藏模型被省略，以及明确的使用授权指引文本。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `spawn_agent_description_lists_visible_models_and_reasoning_efforts` | spawn_agent 描述中包含可见模型名称/slug/描述、默认推理级别、支持的推理级别列表；隐藏模型不出现；包含 "Only use spawn_agent if and only if..." 等授权规则说明 |
