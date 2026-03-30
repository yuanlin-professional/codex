# `event_mapping_tests.rs` — 测试模块

## 测试覆盖范围

事件映射（event_mapping）模块中 `parse_turn_item` 函数，将 ResponseItem 解析为 TurnItem，包括用户消息（含图片/本地图片标签过滤）、助手消息、推理摘要、Hook 提示、Web 搜索调用、会话前缀过滤。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `parses_user_message_with_text_and_two_images` | 解析包含文本和两张图片的用户消息 |
| `skips_local_image_label_text` | 跳过本地图片标签文本 |
| `parses_assistant_message_input_text_for_backward_compatibility` | 为向后兼容解析助手消息的 InputText |
| `skips_unnamed_image_label_text` | 跳过未命名的图片标签文本 |
| `skips_user_instructions_and_env` | 跳过用户指令和环境上下文（AGENTS.md、environment_context、skill、user_shell_command 等） |
| `parses_hook_prompt_message_as_distinct_turn_item` | 将 Hook 提示消息解析为独立的 TurnItem |
| `parses_hook_prompt_and_hides_other_contextual_fragments` | 解析 Hook 提示并隐藏其他上下文片段 |
| `parses_agent_message` | 解析代理消息 |
| `parses_reasoning_summary_and_raw_content` | 解析推理摘要和原始内容 |
| `parses_reasoning_including_raw_content` | 解析包含原始内容的推理项 |
| `parses_web_search_call` | 解析 Web 搜索调用（Search 动作） |
| `parses_web_search_open_page_call` | 解析 Web 搜索打开页面调用 |
| `parses_web_search_find_in_page_call` | 解析 Web 搜索页内查找调用 |
| `parses_partial_web_search_call_without_action_as_other` | 解析无动作的部分 Web 搜索调用为 Other |
