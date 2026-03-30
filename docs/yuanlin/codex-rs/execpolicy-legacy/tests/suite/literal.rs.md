# `literal.rs` — 测试模块

## 测试覆盖范围

测试字面量参数匹配功能，验证策略中定义的固定字符串参数（子命令）能正确匹配或拒绝。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_invalid_subcommand` | 定义 args=["subcommand", "sub-subcommand"] 的自定义策略，验证正确子命令匹配成功，错误子命令返回 LiteralValueDidNotMatch 错误 |
