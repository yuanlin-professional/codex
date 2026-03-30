# `landlock.rs` -- 测试模块

## 测试覆盖范围

覆盖 Linux 沙箱的文件系统读写隔离、网络阻断、超时处理、bubblewrap 特殊场景（`/dev/null` 写入、`/dev/shm` 绑定挂载、缺失可写根目录、`.git`/`.codex` 目录保护、符号链接替换攻击防护）、以及细粒度的 split-policy 文件系统策略执行。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_root_read` | 验证沙箱内可以读取 `/bin` 目录 |
| `test_root_write` | 验证沙箱内禁止写入临时文件（应 panic） |
| `test_dev_null_write` | 验证 bwrap 沙箱内可以写入 `/dev/null` |
| `bwrap_populates_minimal_dev_nodes` | 验证 bwrap 沙箱包含最小化设备节点（null, zero, full, random, urandom, tty） |
| `bwrap_preserves_writable_dev_shm_bind_mount` | 验证 bwrap 沙箱中 `/dev/shm` 可写绑定挂载正常工作 |
| `test_writable_root` | 验证指定可写根目录后可以写入文件 |
| `sandbox_ignores_missing_writable_roots_under_bwrap` | 验证 bwrap 忽略不存在的可写根目录而不报错 |
| `test_no_new_privs_is_enabled` | 验证 `NoNewPrivs` 安全位已启用 |
| `test_timeout` | 验证命令超时后产生 `Sandbox(Timeout)` 错误 |
| `sandbox_blocks_curl` | 验证沙箱阻止 curl 网络请求 |
| `sandbox_blocks_wget` | 验证沙箱阻止 wget 网络请求 |
| `sandbox_blocks_ping` | 验证沙箱阻止 ICMP ping |
| `sandbox_blocks_nc` | 验证沙箱阻止 netcat 连接 |
| `sandbox_blocks_git_and_codex_writes_inside_writable_root` | 验证 `.git` 和 `.codex` 目录即使在可写根目录内也不可写 |
| `sandbox_blocks_codex_symlink_replacement_attack` | 验证通过符号链接指向 `.codex` 的攻击路径被阻止 |
| `sandbox_blocks_explicit_split_policy_carveouts_under_bwrap` | 验证 split-policy 中显式标记为 None 的路径在 bwrap 下被阻止写入 |
| `sandbox_reenables_writable_subpaths_under_unreadable_parents` | 验证不可读父目录下的可写子路径被正确重新启用 |
| `sandbox_blocks_root_read_carveouts_under_bwrap` | 验证根读取策略下的不可读路径在 bwrap 中被正确屏蔽 |
| `sandbox_blocks_ssh` | 验证沙箱阻止 SSH 连接 |
| `sandbox_blocks_getent` | 验证沙箱阻止 DNS 查询（getent） |
| `sandbox_blocks_dev_tcp_redirection` | 验证沙箱阻止 bash `/dev/tcp` 重定向 |
