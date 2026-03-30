# `test_backend.rs` — VT100 测试后端重导出模块

该文件通过 `#[path = "../src/test_backend.rs"]` 属性引入 TUI 源码中的 `test_backend.rs` 实现，并将 `VT100Backend` 重新导出供集成测试使用。`VT100Backend` 是一个包装了 `CrosstermBackend<vt100::Parser>` 的虚拟终端后端，用于在不依赖真实终端的情况下进行 TUI 渲染测试。
