# `paths.rs` — 文件修改时间工具函数
该文件提供 `file_modified_time_utc` 异步函数，用于获取指定路径的文件修改时间并转换为 `DateTime<Utc>`（精度截断至秒）。当文件不存在或元数据读取失败时返回 `None`。
