# `no_panic_on_startup.rs` — 测试模块

## 测试覆盖范围

回归测试（关联 issue #8803）：确保在 rules 目录为非法格式（如将 `rules` 创建为文件而非目录）的情况下，Codex CLI 启动时不会发生 panic，而是优雅地输出错误信息并以非零退出码退出。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `malformed_rules_should_not_panic` | 在临时 `CODEX_HOME` 中将 `rules` 创建为文件（而非目录），配置 ollama 作为 model_provider，通过 PTY 启动 Codex CLI。测试验证：(1) 退出码非零；(2) 输出包含 "ERROR: Failed to initialize codex:" 错误信息；(3) 输出包含 "failed to read rules files" 提示。该测试当前标记为 `#[ignore]`（标注为 flaky）。Windows 平台因 PTY 限制跳过。 |
