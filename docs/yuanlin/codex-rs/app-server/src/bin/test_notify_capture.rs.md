# `bin/test_notify_capture.rs` — 测试用通知捕获二进制工具

本文件是 `notify_capture` 的简化测试版本，同样接受输出路径和 payload 两个参数，通过先写临时文件（`.json.tmp` 后缀）再原子重命名的方式实现安全写入。与正式版本相比，代码更精简，使用 `std::fs::write` 替代了手动的文件创建/写入/同步流程。
