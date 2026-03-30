# `original_image_detail_tests.rs` — 测试模块

## 测试覆盖范围

测试图片详情级别（image detail）的规范化逻辑，验证 `can_request_original_image_detail` 和 `normalize_output_image_detail` 在不同特性开关和模型支持组合下的行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `image_detail_original_feature_enables_explicit_original_without_force` | 当特性开关 `ImageDetailOriginal` 已启用且模型支持时，可以请求 `Original` 级别；显式传入 `Original` 保留，不传入则返回 `None` |
| `explicit_original_is_dropped_without_feature_or_model_support` | 当特性开关未启用或模型不支持 `Original` 级别时，显式传入的 `Original` 被丢弃为 `None` |
| `unsupported_non_original_detail_is_dropped` | 即使特性和模型都支持 `Original`，传入 `Low` 等非 `Original` 级别也会被丢弃为 `None` |
