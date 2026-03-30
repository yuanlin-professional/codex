# `turn_steer.rs` — 测试模块

## 测试覆盖范围
测试 `turn/steer` JSON-RPC 方法，验证需要活跃 turn、拒绝超大文本输入，以及返回活跃 turn ID。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `turn_steer_requires_active_turn` | 需要活跃的 turn 才能 steer |
| `turn_steer_rejects_oversized_text_input` | 拒绝超大文本输入 |
| `turn_steer_returns_active_turn_id` | 返回当前活跃的 turn ID |
