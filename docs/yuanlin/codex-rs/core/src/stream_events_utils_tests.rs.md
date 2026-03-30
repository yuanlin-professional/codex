# `stream_events_utils_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖流事件处理工具函数，包括非工具响应项（assistant 消息）中引用标记（oai-mem-citation）的剥离与解析、计划块（proposed_plan）的隐藏处理、图片生成结果的保存（base64 解码写入 PNG）、data URL 和非法 base64 的拒绝，以及 call_id 路径注入的清理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `handle_non_tool_response_item_strips_citations_from_assistant_message` | 验证 assistant 消息中的 oai-mem-citation 标记被剥离，文本和引用信息被正确解析 |
| `last_assistant_message_from_item_strips_citations_and_plan_blocks` | 验证同时剥离引用标记和计划块后返回清洁文本 |
| `last_assistant_message_from_item_returns_none_for_citation_only_message` | 验证仅含引用标记的消息剥离后返回 None |
| `last_assistant_message_from_item_returns_none_for_plan_only_hidden_message` | 验证仅含计划块的消息在计划模式下返回 None |
| `save_image_generation_result_saves_base64_to_png_in_codex_home` | 验证 base64 图片数据正确解码并保存为 PNG 文件 |
| `save_image_generation_result_rejects_data_url_payload` | 验证 data URL 格式的图片数据被拒绝 |
| `save_image_generation_result_overwrites_existing_file` | 验证保存图片时覆盖已存在的文件 |
| `save_image_generation_result_sanitizes_call_id_for_codex_home_output_path` | 验证 call_id 中的路径遍历字符（../）被清理 |
| `save_image_generation_result_rejects_non_standard_base64` | 验证非标准 base64 编码（URL-safe 变体）被拒绝 |
| `save_image_generation_result_rejects_non_base64_data_urls` | 验证非 base64 的 data URL（如 SVG）被拒绝 |
