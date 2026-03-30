# `test_client_rpc_methods.py` -- 测试模块

## 测试覆盖范围

验证 `AppServerClient` 的 RPC 方法行为，包括方法名称正确性、参数序列化、通知类型解析和回退处理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_thread_set_name_and_compact_use_current_rpc_methods` | 验证 set_name 使用 "thread/name/set"、compact 使用 "thread/compact/start" 方法名 |
| `test_generated_params_models_are_snake_case_and_dump_by_alias` | 验证生成的参数模型支持 snake_case 输入、camelCase 序列化输出 |
| `test_generated_v2_bundle_has_single_shared_plan_type_definition` | 验证 v2_all.py 中 PlanType 类只有一个定义 |
| `test_notifications_are_typed_with_canonical_v2_methods` | 验证通知可正确反序列化为带类型的 Pydantic 模型 |
| `test_unknown_notifications_fall_back_to_unknown_payloads` | 验证未知通知方法回退为 UnknownNotification |
| `test_invalid_notification_payload_falls_back_to_unknown` | 验证无效通知载荷回退为 UnknownNotification |
