# `storage_tests.rs` — 测试模块

## 测试覆盖范围
验证所有认证存储后端（File/Keyring/Auto/Ephemeral）的加载、保存和删除操作。使用 MockKeyringStore 模拟 keyring 行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `file_storage_load_returns_auth_dot_json` | 文件存储正常加载 |
| `file_storage_save_persists_auth_dot_json` | 文件存储正常持久化 |
| `file_storage_delete_removes_auth_file` | 文件存储删除 |
| `ephemeral_storage_save_load_delete_is_in_memory_only` | 临时存储仅在内存中 |
| `keyring_auth_storage_load_returns_deserialized_auth` | Keyring 存储正常加载 |
| `keyring_auth_storage_save_persists_and_removes_fallback_file` | Keyring 保存并移除回退文件 |
| `auto_auth_storage_load_prefers_keyring_value` | Auto 模式优先使用 keyring |
| `auto_auth_storage_save_falls_back_when_keyring_errors` | Auto 模式 keyring 失败回退到文件 |
