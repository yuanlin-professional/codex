# `export.rs` — TypeScript/JSON Schema 代码生成CLI

`export.rs` 是一个 CLI 二进制入口，使用 clap 解析命令行参数（输出目录、Prettier 路径、是否包含实验性 API），调用 `codex_app_server_protocol` 库的 `generate_ts_with_options` 和 `generate_json_with_experimental` 函数，将 app-server 协议类型导出为 TypeScript 绑定和 JSON Schema 文件。
