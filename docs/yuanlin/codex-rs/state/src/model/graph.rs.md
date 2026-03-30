# `graph.rs` — 线程生成边状态枚举
该文件定义了 `DirectionalThreadSpawnEdgeStatus` 枚举，表示父子线程生成边的方向性状态，包含 `Open`（活跃）和 `Closed`（已关闭）两个变体。使用 `strum` 宏支持序列化为 snake_case 字符串。
