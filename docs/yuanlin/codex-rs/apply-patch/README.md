# apply-patch

## 功能概述

`codex-apply-patch` 是 Codex 项目的补丁应用模块，实现了一种自定义的补丁格式解析和应用引擎。它支持文件的添加、删除、更新和移动操作，可以作为库被其他 crate 调用，也提供了独立的命令行二进制工具 `apply_patch`。该模块是 Codex AI 代理进行代码修改的核心基础设施。

## 架构说明

补丁处理分为三个阶段：

### 1. 解析阶段（parser）
解析自定义补丁格式（以 `*** Begin Patch` / `*** End Patch` 包裹），支持以下 Hunk 类型：
- `*** Add File: <path>` - 添加新文件
- `*** Delete File: <path>` - 删除文件
- `*** Update File: <path>` - 更新文件（可带 `*** Move to: <dest>` 实现移动）

更新块使用 `@@` 分隔的差异段（chunks），每行以 `+`（添加）、`-`（删除）或 ` `（上下文）前缀标识。

### 2. 匹配阶段（seek_sequence）
在原始文件中定位需要修改的区域，使用模糊匹配机制：
- 先尝试精确匹配
- 支持 Unicode 标点规范化（如 EN DASH -> ASCII dash）
- 处理文件末尾空行的差异

### 3. 应用阶段
将解析后的 Hunk 应用到文件系统：
- 计算替换位置（compute_replacements）
- 按逆序应用替换（apply_replacements）避免位置偏移
- 创建必要的父目录
- 输出 git 风格的变更摘要（A/M/D）

### 独立可执行文件
通过 `codex-arg0` 模块的 arg0 分发技巧，`apply_patch` 可以作为 Codex CLI 的子命令被调用，无需单独部署。

## 目录结构

```
apply-patch/
  Cargo.toml
  apply_patch_tool_instructions.md   # GPT-4.1 工具使用说明
  src/
    lib.rs                           # 模块入口，核心应用逻辑
    main.rs                          # 独立 CLI 入口
    parser.rs                        # 补丁格式解析器
    seek_sequence.rs                 # 序列匹配算法
    invocation.rs                    # heredoc 提取和调用解析
    standalone_executable.rs         # 独立可执行文件支持
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `anyhow` | 错误处理 |
| `similar` | unified diff 生成（TextDiff） |
| `thiserror` | 错误类型定义 |
| `tree-sitter` / `tree-sitter-bash` | Bash heredoc 语法解析 |

## 核心接口/API

### 主要函数

```rust
// 解析补丁文本
pub fn parse_patch(patch: &str) -> Result<ParsedPatch, ParseError>;

// 应用补丁到文件系统
pub fn apply_patch(patch: &str, stdout: &mut impl Write, stderr: &mut impl Write) -> Result<(), ApplyPatchError>;

// 应用解析后的 Hunk 列表
pub fn apply_hunks(hunks: &[Hunk], stdout: &mut impl Write, stderr: &mut impl Write) -> Result<(), ApplyPatchError>;

// 从 Hunk 生成 unified diff
pub fn unified_diff_from_chunks(path: &Path, chunks: &[UpdateFileChunk]) -> Result<ApplyPatchFileUpdate, ApplyPatchError>;

// 验证 argv 是否为 apply_patch 调用
pub fn maybe_parse_apply_patch_verified(argv: &[String]) -> MaybeApplyPatchVerified;
```

### 类型定义

- **`Hunk`** - 补丁块枚举（`AddFile` / `DeleteFile` / `UpdateFile`）
- **`ApplyPatchError`** - 错误类型（`ParseError` / `IoError` / `ComputeReplacements` / `ImplicitInvocation`）
- **`ApplyPatchAction`** - 补丁应用动作，包含文件变更映射
- **`ApplyPatchFileChange`** - 文件变更类型（`Add` / `Delete` / `Update`）
- **`AffectedPaths`** - 受影响路径汇总（added / modified / deleted）

### 常量

- `APPLY_PATCH_TOOL_INSTRUCTIONS` - 嵌入的 GPT-4.1 工具使用说明文档
- `CODEX_CORE_APPLY_PATCH_ARG1` - 自调用标识参数 `"--codex-run-as-apply-patch"`
