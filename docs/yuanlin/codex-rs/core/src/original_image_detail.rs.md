# `original_image_detail.rs` — 原始图片细节级别判断与规范化

该文件提供两个函数用于处理图片细节级别（`ImageDetail`）。`can_request_original_image_detail` 检查模型是否支持原始图片细节且特性开关已启用。`normalize_output_image_detail` 对输出的图片细节级别进行规范化：仅当模型支持且特性启用时保留 `Original` 级别，否则一律返回 `None`。依赖 `codex_features` 进行特性开关判断，依赖 `codex_protocol` 的模型信息。对应的测试位于 `original_image_detail_tests.rs` 文件中。
