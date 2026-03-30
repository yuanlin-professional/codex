# `sandbox_tags_tests.rs` — 沙箱标签生成逻辑测试

测试 `sandbox_tag` 函数在不同沙箱策略下返回的指标标签。验证 `DangerFullAccess` 策略始终返回 `"none"`、`ExternalSandbox` 策略返回 `"external"`、以及默认只读策略根据当前平台的实际沙箱类型返回对应标签。
