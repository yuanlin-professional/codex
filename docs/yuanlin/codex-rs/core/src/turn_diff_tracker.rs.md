# `turn_diff_tracker.rs` — 回合级文件变更差异追踪器

## 功能概述

该文件实现了 `TurnDiffTracker`，用于在一个对话回合中追踪所有通过 `apply_patch` 修改的文件变更，并生成聚合的 unified diff 输出。其工作原理是：在每个文件首次被修改前，通过内存快照保存其基线内容和元数据（文件模式、Git blob OID）；之后通过比较基线快照与磁盘当前状态，使用 `similar` crate 在纯内存中计算统一差异。支持文件的添加、删除、修改和重命名追踪，输出格式与 `git diff` 兼容。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TurnDiffTracker` | struct | 回合差异追踪器主体，维护路径映射、基线快照和 Git 根缓存 |
| `BaselineFileInfo` | struct (内部) | 基线文件信息：路径、内容字节、文件模式、Git blob OID |
| `FileMode` | enum (内部) | 文件模式：Regular(100644) / Executable(100755) / Symlink(120000) |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `TurnDiffTracker::on_patch_begin` | `fn(&mut self, changes: &HashMap<PathBuf, FileChange>)` | 在补丁应用前调用，为首次出现的文件建立基线快照 |
| `TurnDiffTracker::get_unified_diff` | `fn(&mut self) -> Result<Option<String>>` | 计算并返回所有追踪文件的聚合 unified diff |
| `get_file_diff` | `fn(&mut self, internal_file_name: &str) -> String` | 计算单个文件的 unified diff（含 header 和 hunk） |
| `find_git_root_cached` | `fn(&mut self, start: &Path) -> Option<PathBuf>` | 带缓存的 Git 仓库根目录查找 |
| `git_blob_oid_for_path` | `fn(&mut self, path: &Path) -> Option<String>` | 调用 `git hash-object` 获取文件的 blob SHA-1 |
| `relative_to_git_root_str` | `fn(&mut self, path: &Path) -> String` | 计算相对于 Git 根的路径字符串 |
| `git_blob_sha1_hex_bytes` | `fn(data: &[u8]) -> Output<sha1::Sha1>` | 纯 Rust 计算 Git blob 对象的 SHA-1 |

## 依赖关系

- **引用的 crate**: `similar`（文本差异计算）、`sha1`（Git blob 哈希）、`uuid`（内部文件名生成）、`anyhow`
- **被引用方**: `crate::protocol::FileChange`（补丁变更类型）、会话的 apply_patch 工具实现

## 实现备注

- 每个外部文件路径映射到一个 UUID 内部名称，用于稳定追踪重命名/移动操作。
- 对于新增文件（磁盘上不存在），基线内容为空且 OID 为全零（`0000000000000000000000000000000000000000`），使 diff 输出为标准的 `new file mode` 格式。
- Git blob OID 优先通过 `git hash-object` 命令获取（保证与 Git 行为一致），失败时回退到内置的 `git_blob_sha1_hex_bytes` 纯 Rust 实现。
- `git_root_cache` 缓存已发现的 Git 根目录路径，避免重复的文件系统遍历。
- 二进制文件（UTF-8 解码失败）使用 "Binary files differ" 占位符。
- 符号链接以目标路径字节作为内容进行差异比较。
- Windows 上路径分隔符 `\` 会被统一替换为 `/`，保持与 Git 输出格式一致。
