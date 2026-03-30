# `mitm_tests.rs` -- 测试模块

## 测试覆盖范围
测试 MITM 中间人代理的策略执行逻辑，包括 Limited 模式下的方法阻断、主机头不匹配拒绝和 CONNECT 后的本地/私有地址二次检查。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `mitm_policy_blocks_disallowed_method_and_records_telemetry` | Limited 模式下 POST 请求被阻断，验证返回 403 和遥测记录 |
| `mitm_policy_rejects_host_mismatch` | 请求 Host 头与 CONNECT 目标不匹配时返回 400，不记录遥测 |
| `mitm_policy_rechecks_local_private_target_after_connect` | CONNECT 建立后对本地/私有目标地址重新检查，防止 DNS 重绑定 |
