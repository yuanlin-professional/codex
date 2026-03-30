# `lib.rs` — 云任务客户端模块入口

`cloud-tasks-client` crate 的入口文件，导出云任务 API 类型和 trait。通过 feature flag 条件编译提供 `MockClient`（mock feature）和 `HttpClient`（online feature）两种后端实现。
