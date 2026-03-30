# `lib.rs` — app-server-protocol crate 入口

app-server-protocol crate 的根模块，声明并导出所有子模块（`experimental_api`、`export`、`jsonrpc_lite`、`protocol`、`schema_fixtures`），并通过 `pub use` 大量 re-export 类型。导出的类型涵盖 v1 遗留 API（Initialize、ConversationSummary、ExecCommandApproval 等）和 v2 新 API（通过 `protocol::v2::*` 通配导出），以及 Schema fixture 管理函数和代码生成工具函数。
