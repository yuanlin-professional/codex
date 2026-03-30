# `decision.rs` — 策略决策类型

`Decision` 枚举定义了策略检查的三种决策结果：`Allow`（允许执行）、`Prompt`（需要用户确认）和 `Forbidden`（禁止执行）。实现了 `Ord` trait 以支持严格性排序（Allow < Prompt < Forbidden），使得多条规则匹配时取最严格的决策。`parse` 方法从字符串 `"allow"`/`"prompt"`/`"forbidden"` 解析为对应枚举值。
