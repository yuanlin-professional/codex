# `dynamic_tools.rs` — 测试模块

## 测试覆盖范围
测试动态工具（dynamic tools）注入功能，验证线程启动时动态工具被注入到模型请求中、
隐藏的动态工具不包含在模型请求中，以及动态工具调用往返（round-trip）时正确传递文本和富内容项。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_start_injects_dynamic_tools_into_model_requests` | 动态工具正确注入模型请求 |
| `thread_start_keeps_hidden_dynamic_tools_out_of_model_requests` | 隐藏的动态工具不出现在模型请求中 |
| `dynamic_tool_call_round_trip_sends_text_content_items_to_model` | 动态工具调用往返正确发送文本内容 |
| `dynamic_tool_call_round_trip_sends_content_items_to_model` | 动态工具调用往返正确发送富内容项 |
