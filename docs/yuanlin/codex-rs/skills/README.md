# skills

## 功能概述

`codex-skills` 是 Codex 项目中系统级嵌入式技能的管理 crate。它负责将编译时内嵌的系统技能资源(sample skills)安装到磁盘缓存目录,并通过指纹机制实现增量更新。

与 `codex-core-skills`(运行时技能发现和管理)不同,本 crate 专注于系统技能的资源嵌入和安装,是技能系统的底层基础设施。

核心职责包括:

- **嵌入式资源管理**: 使用 `include_dir!` 宏在编译时将 `src/assets/samples/` 目录下的系统技能打包到二进制文件中。
- **磁盘安装**: 将嵌入资源释放到 `$CODEX_HOME/skills/.system/` 目录。
- **指纹增量更新**: 通过哈希指纹检测嵌入资源是否有变更,仅在变更时重新安装。

## 架构说明

该 crate 仅有一个 `lib.rs` 文件,整体流程如下:

1. **编译时嵌入**:
   - 通过 `include_dir!("$CARGO_MANIFEST_DIR/src/assets/samples")` 将系统技能目录嵌入到二进制。
   - 通过 `build.rs` 进行编译时处理。

2. **安装流程** (`install_system_skills()`):
   - 计算嵌入目录的指纹: 遍历所有文件,对路径和内容计算 `DefaultHasher` 哈希,加上 salt(`v1`)。
   - 检查目标目录的 `.codex-system-skills.marker` 标记文件,若指纹匹配则跳过安装。
   - 若指纹不匹配或标记不存在,则删除旧目录并全量写入新资源。
   - 写入新的标记文件。

3. **目录规范**:
   - 系统技能缓存根目录: `$CODEX_HOME/skills/.system/`
   - 标记文件: `$CODEX_HOME/skills/.system/.codex-system-skills.marker`
   - 嵌入资源包含如 `skill-creator/SKILL.md` 等系统级技能。

4. **错误处理**: 定义 `SystemSkillsError` 枚举,包含带上下文的 I/O 错误。

## 目录结构

```
skills/
  Cargo.toml
  build.rs                      # 编译时构建脚本
  src/
    lib.rs                      # 全部实现:嵌入、安装、指纹、测试
    assets/
      samples/                  # 嵌入的系统技能资源
        skill-creator/
          SKILL.md              # 技能创建器示例
          scripts/
            init_skill.py       # 初始化脚本
        ...                     # 其他系统技能
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-utils-absolute-path` | `AbsolutePathBuf` 路径安全操作 |
| `include_dir` | 编译时目录嵌入 |
| `thiserror` | 错误类型派生 |

## 核心接口/API

### 安装

```rust
/// 将嵌入的系统技能安装到磁盘缓存
/// 通过指纹检测跳过未变更的安装
pub fn install_system_skills(codex_home: &Path) -> Result<(), SystemSkillsError>;

/// 获取系统技能缓存根目录路径
/// 通常为 $CODEX_HOME/skills/.system
pub fn system_cache_root_dir(codex_home: &Path) -> PathBuf;
```

### 错误类型

```rust
pub enum SystemSkillsError {
    Io {
        action: &'static str,
        source: std::io::Error,
    },
}
```
