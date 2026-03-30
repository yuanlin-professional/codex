# `items.rs` — 测试模块

## 测试覆盖范围
事件项（Items）的发射与流式处理，包括用户消息、助手消息、推理项、Web 搜索、图片生成、内容增量、计划模式下的流式解析与引用剥离等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `user_message_item_is_emitted` | 用户消息项正确发射 ItemStarted 和 ItemCompleted 事件 |
| `assistant_message_item_is_emitted` | 助手消息项正确发射 ItemStarted 和 ItemCompleted 事件 |
| `reasoning_item_is_emitted` | 推理项正确发射，包含摘要和原始推理内容 |
| `web_search_item_is_emitted` | Web 搜索项正确发射 WebSearchBegin 和 ItemCompleted 事件 |
| `image_generation_call_event_is_emitted` | 图片生成调用事件正确发射，图片保存到临时目录 |
| `image_generation_call_event_is_emitted_when_image_save_fails` | 图片保存失败时，图片生成事件仍然发射但 saved_path 为 None |
| `agent_message_content_delta_has_item_metadata` | 助手消息内容增量包含正确的线程 ID、轮次 ID 和项 ID 元数据 |
| `plan_mode_emits_plan_item_from_proposed_plan_block` | 计划模式下从 proposed_plan 标签中正确提取并发射计划项 |
| `plan_mode_strips_plan_from_agent_messages` | 计划模式下从代理消息中剥离计划内容 |
| `plan_mode_streaming_citations_are_stripped_across_added_deltas_and_done` | 计划模式流式处理中引用标签被正确剥离 |
| `plan_mode_streaming_proposed_plan_tag_split_across_added_and_delta_is_parsed` | proposed_plan 标签跨 added 和 delta 消息分割时仍能正确解析 |
| `plan_mode_handles_missing_plan_close_tag` | 计划模式处理缺少关闭标签的 proposed_plan |
| `reasoning_content_delta_has_item_metadata` | 推理内容增量包含正确的项 ID 元数据 |
| `reasoning_raw_content_delta_respects_flag` | 启用原始推理显示标志后，正确发射 ReasoningRawContentDelta 事件 |
