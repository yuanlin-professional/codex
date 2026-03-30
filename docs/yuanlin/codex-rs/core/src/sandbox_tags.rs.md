# `sandbox_tags.rs` — 沙箱类型指标标签生成

该文件提供 `sandbox_tag` 函数，根据沙箱策略和 Windows 沙箱级别返回用于指标上报的静态字符串标签。对于 `DangerFullAccess` 策略返回 `"none"`，外部沙箱返回 `"external"`，Windows 提权模式返回 `"windows_elevated"`，其他情况通过 `get_platform_sandbox` 获取平台沙箱类型并返回对应的指标标签，无可用沙箱时回退为 `"none"`。对应的测试位于 `sandbox_tags_tests.rs` 文件中。
