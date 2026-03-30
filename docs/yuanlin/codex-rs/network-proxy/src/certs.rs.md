# `certs.rs` -- MITM CA 证书管理

## 功能概述
管理 MITM 中间人代理所需的 CA 证书，包括在 `CODEX_HOME/proxy/` 目录下自动生成或加载 CA 证书和私钥，以及按需为目标主机签发叶证书。CA 使用 ECDSA P-256 算法，私钥文件严格限制权限（Unix 0o600）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ManagedMitmCa` | struct | 受管理的 MITM CA，持有签发者（issuer）用于签发叶证书 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ManagedMitmCa::load_or_create` | `fn() -> Result<Self>` | 加载已有 CA 或生成新 CA |
| `tls_acceptor_data_for_host` | `fn(&self, host) -> Result<TlsAcceptorData>` | 为指定主机签发叶证书并构建 TLS 接受器 |
| `write_atomic_create_new` | `fn(path, contents, mode) -> Result<()>` | 原子写入文件（硬链接或重命名），不覆盖已有文件 |
| `validate_existing_ca_key_file` | `fn(path) -> Result<()>` | 验证 CA 私钥文件安全性（非符号链接、权限 <= 600） |

## 依赖关系
- **引用的 crate**: `rcgen`, `rustls`, `rama_tls_rustls`, `codex_utils_home_dir`
- **被引用方**: `mitm.rs` 使用 `ManagedMitmCa`

## 实现备注
- 叶证书支持域名和 IP 地址的 SAN
- 原子写入使用 hard_link 防止覆盖已有文件
- CA 证书路径：`CODEX_HOME/proxy/ca.pem`，私钥：`CODEX_HOME/proxy/ca.key`
