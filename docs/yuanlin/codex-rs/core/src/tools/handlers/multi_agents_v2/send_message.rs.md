# `send_message.rs` — V2 发送消息处理器（仅排队）

该文件实现了 V2 多代理协作系统中的 `send_message` 工具处理器。它是 `assign_task` 的对应物，使用 `MessageDeliveryMode::QueueOnly` 模式，仅将消息排入目标代理的通信队列而不立即触发新的 turn。实际逻辑完全委托给 `message_tool::handle_message_tool` 共享实现。Handler 结构体实现了 `ToolHandler` trait，输出类型为 `MessageToolResult`。
