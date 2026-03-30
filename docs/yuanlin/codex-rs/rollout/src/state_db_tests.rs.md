# `state_db_tests.rs` — 测试模块

## 测试覆盖范围
该文件测试 StateDb 辅助功能，主要验证 cursor 到 anchor 的时间戳格式规范化逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `cursor_to_anchor_normalizes_timestamp_format` | 验证分页游标中的时间戳格式被正确规范化为 DateTime |
