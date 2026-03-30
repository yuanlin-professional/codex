# `timing.rs` — 测试模块

## 测试覆盖范围
该文件测试指标系统的时间记录功能，包括 `record_duration` 直方图记录和 `start_timer` 自动计时器。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `record_duration_records_histogram` | 持续时间记录映射到带 ms 单位的直方图 |
| `timer_result_records_success` | Timer 在 Drop 时自动记录耗时到直方图 |
