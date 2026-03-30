# `serde_helpers.rs` — 序列化辅助函数

提供 `deserialize_double_option` 和 `serialize_double_option` 两个辅助函数，用于处理 `Option<Option<T>>` 类型的序列化/反序列化。这种双层 Option 模式用于区分"字段未出现"（外层 None）、"字段值为 null"（`Some(None)`）和"字段有值"（`Some(Some(v))`）三种语义，通过委托 `serde_with::rust::double_option` 实现。
