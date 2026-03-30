# secrets

## 功能概述

`codex-secrets` 是 Codex 项目的密钥管理模块，提供安全的密钥存储、检索、删除和列举功能。它支持全局和环境级别的密钥作用域，使用 age 加密算法对密钥进行本地加密存储，并借助系统钥匙串（Keyring）保护主密钥。此外还提供了密钥脱敏（redact）工具，用于在日志和输出中自动遮蔽敏感信息。

## 架构说明

该模块采用分层架构：

1. **SecretsManager** - 高层管理接口，面向外部使用者，封装后端实现细节。
2. **SecretsBackend trait** - 密钥存储后端抽象接口，定义 `set/get/delete/list` 四个核心操作。
3. **LocalSecretsBackend** - 本地存储后端实现，将加密后的密钥存储在 `codex_home` 目录下，使用 `age` 加密库进行加解密。
4. **KeyringStore** - 系统钥匙串抽象（来自 `codex-keyring-store`），用于安全存储 age 加密所需的主密钥。

### 密钥作用域

- **SecretScope::Global** - 全局密钥，所有环境共享
- **SecretScope::Environment(id)** - 环境级密钥，通常与 Git 仓库绑定

环境 ID 的生成逻辑：优先使用 Git 仓库根目录名称，否则使用工作目录路径的 SHA-256 哈希前 12 位。

### 密钥命名规则

`SecretName` 仅允许大写字母（A-Z）、数字（0-9）和下划线（_），例如 `GITHUB_TOKEN`。

## 目录结构

```
secrets/
  Cargo.toml
  src/
    lib.rs         # 模块入口，SecretsManager、SecretName、SecretScope 定义
    local.rs       # LocalSecretsBackend 实现（age 加密 + 文件存储）
    sanitizer.rs   # 密钥脱敏工具（redact_secrets）
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `age` | 密钥加密/解密（modern encryption） |
| `base64` | 编码处理 |
| `codex-git-utils` | 获取 Git 仓库根目录用于环境 ID 推断 |
| `codex-keyring-store` | 系统钥匙串访问，保护主密钥 |
| `rand` | 随机数生成 |
| `regex` | 正则匹配（用于脱敏） |
| `schemars` | JSON Schema 生成 |
| `serde` / `serde_json` | 序列化 |
| `sha2` | SHA-256 哈希（用于 keyring account 和环境 ID 计算） |
| `tracing` | 日志追踪 |

## 核心接口/API

### SecretsManager

```rust
impl SecretsManager {
    pub fn new(codex_home: PathBuf, backend_kind: SecretsBackendKind) -> Self;
    pub fn set(&self, scope: &SecretScope, name: &SecretName, value: &str) -> Result<()>;
    pub fn get(&self, scope: &SecretScope, name: &SecretName) -> Result<Option<String>>;
    pub fn delete(&self, scope: &SecretScope, name: &SecretName) -> Result<bool>;
    pub fn list(&self, scope_filter: Option<&SecretScope>) -> Result<Vec<SecretListEntry>>;
}
```

### 类型定义

- **`SecretName`** - 密钥名称（大写字母 + 数字 + 下划线）
- **`SecretScope`** - 密钥作用域：`Global` / `Environment(String)`
- **`SecretListEntry`** - 列表条目，包含 scope 和 name
- **`SecretsBackendKind`** - 后端类型枚举，当前仅支持 `Local`

### 工具函数

- **`environment_id_from_cwd(cwd: &Path) -> String`** - 从当前目录推断环境 ID
- **`redact_secrets(text: &str) -> String`** - 从文本中脱敏已知密钥值
